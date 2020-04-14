# Lua

----
## 元表
|Mode|描述|
|--|--|
|__add|加|
|__sub|减|
|__mul|乘|
|__div|除|
|__mod|取余|
|__unm|取相反数|
|__concat|连接|
|__eq|等于|
|__lt|小于|
|__le|小于等于|
|__call|将table作为方法调用时会调用元表的__call|
|__tostring|将table作为string传入时会调用元表的__tostring|
|__index|访问一个table的字段时，如果table有这个字段，直接返回对应的值；当table没有这个字段，就会去找table的元表的__index，没有找到__index返回nil。如果有的话分两种情况：__index可以是方法，也可以是一个table。|
|__newindex|给table的key赋值时（注意原先没有改key的值），调用此方法。|

**实例：**
```lua
-- 数据追踪

local DataTracker = {};

DataTracker._t = {};

function DataTracker:trackingDataModify(obj,trackers,isAssert)

	--未传obj或trackers，直接return，不执行以下逻辑
	if (not obj) or (not trackers) then
		return;
	end

	--判断是否需要设置元表
	local isSetMetatable = false;
	if not obj._trackers then
		obj._trackers = {};
		isSetMetatable = true;
	end
	--保存新增监控键值
	for _,tv in pairs(trackers) do
		self._t[obj] = self._t[obj] or {};
		if not self._t[obj][tv] then
			self._t[obj][tv] = {_times = 1, _isAssert = isAssert};
		else
			self._t[obj][tv]._isAssert = isAssert;
		end
		if not obj._trackers[tv] then
			obj._trackers[tv] = obj[tv];
		end
		obj[tv] = nil;
	end

	if isSetMetatable then
		--设置元表
		local mt = {
			__index = function(t,k)
				if t._trackers and t._trackers[tv] then
					return t._trackers[tv];
				else
					return rawget(t,k);
				end
			end,
			__newindex = function(t,k,v)
				if self._t[obj] and self._t[obj][k] and self._t[obj][k]._times then
					if not self._t[obj][k]._isAssert then
						print(string.format("第%d次修改键值为%s的字段，修改前值为：%s，修改后值为：%s。%s\n",
							self._t[obj][k]._times,
							tostring(k),
							tostring(t._trackers[k]), 
							tostring(v),
							debug.traceback("",2)
						));
						self._t[obj][k]._times = self._t[obj][k]._times + 1;
						t._trackers[k] = v;
					else
						assert(false,"第"..tostring(self._t[obj][k]._times or 1).."次修改键值为“"..tostring(k).."”的字段，此次你没权限修改！！");
					end
				else
					if t._trackers and t._trackers[k] then
						t._trackers[k]= nil;
					end
					rawset(t,k,v);
				end
			end
		};
		setmetatable(obj, mt);
	end
end

return DataTracker;
```


## 协程
|方法|描述|
| -- | -- |
|coroutine.create()|创建 coroutine，返回 coroutine， 参数是一个函数，当和 resume 配合使用的时候就唤醒函数调用|
|coroutine.resume()|重启 coroutine，和 create 配合使用|
|coroutine.yield()|挂起 coroutine，将 coroutine 设置为挂起状态，这个和 resume 配合使用能有很多有用的效果|
|coroutine.status()|查看 coroutine 的状态。注意：coroutine 的状态有三种：dead，suspended，running，具体什么时候有这样的状态请参考下面的程序|
|coroutine.wrap（）|创建 coroutine，返回一个函数，一旦你调用这个函数，就进入 coroutine，和 create 功能重复|
|coroutine.running()|返回正在跑的 coroutine，一个 coroutine 就是一个线程，当使用running的时候，就是返回一个 corouting 的线程号|

**实际使用：**  
```lua
-- tasklet.lua

local M = {}

local function resume_task(task, ...)
    if task.paused then
        task.pending_args = {...}
        return
    end
    local success
    success, task.action = coroutine.resume(task.coroutine, ...)
    if not success then
        local msg = debug.traceback(task.coroutine, task.action)
        print(msg)
    elseif task.action then
        task.action(function(...)
            resume_task(task, ...)
        end)
        return
    end
end

---
-- 启动微线程。
-- @function [parent=#tasklet] spawn
-- @param #function fn
-- @param ... 传给fn的参数。
-- @usage
-- tasklet.spawn(function(arg1, arg2)
--     while true do
--         local dt = tasklet.sleep(0.1)
--         sprite.x = sprite.x + dt * 10
--     end
-- end, arg1, arg2)
function M.spawn(fn, ...)
    local co = coroutine.create(fn)
    local task = {
        coroutine = co,
    }
    resume_task(task, ...)
    return task
end
---
-- 停止微线程。
-- @function [parent=#tasklet] cancel
function M.cancel(task)
    if not task.action then
        return 'invalid task status'
    end
    if type(task.action) == 'table' and task.action.cancel then
        task.action:cancel()
    end
    task.paused = true
    task.action = nil
end

---
-- 暂停微线程。
-- @function [parent=#tasklet] pause
function M.pause(task)
    assert(task.action, 'invalid task status')
    if type(task.action) == 'table' and task.action.pause then
        task.action:pause()
    else
        task.paused = true
    end
end

---
-- 恢复微线程。
-- @function [parent=#tasklet] resume
function M.resume(task)
    assert(task.action, 'invalid task status')
    if type(task.action) == 'table' and task.action.resume then
        task.action:resume()
    else
        resume_task(task, unpack(task.pending_args))
    end
end

---
-- 在微线程中执行，暂停 n 秒
-- @function [parent=#tasklet] sleep
-- @param #number n
function M.sleep(n)
    -- sleep action
    return coroutine.yield(setmetatable({
        _handler = nil,
        cancel = function(self)
            if self._handler then
                self._handler:cancel() -- 取消定时器
            end
        end,
        pause = function(self)
            if self._handler then
                self._handler.paused = true -- 暂停定时器
                return true
            end
            return false
        end,
        resume = function(self)
            if self._handler then
                self._handler.paused = false -- 恢复定时器
                return true
            end
            return false
        end,
    }, {
        __call = function(self, callback)
            self._handler = schedule_once(callback, n) -- n毫秒后执行一次callback，返回定时器句柄
        end
    }))
end

return M
```

## Lua和C++
Lua和C++语言通信的主要方法是：一个无处不在的虚拟栈（先进后出）。

### lua提供了C API对栈进行操作
```c++
#include <iostream>
#include <string.h>
using namespace std;
 
extern "C" {
	#include "lua.h"
	#include "lauxlib.h"
	#include "lualib.h"
}
void main() {
	//1.创建一个state
	lua_State *L = luaL_newstate();
	
	//2.入栈操作
	lua_pushstring(L, "I am so cool~"); 
	lua_pushnumber(L,20);
 
	//3.取值操作
	if( lua_isstring(L,1)){            	//判断是否可以转为string
		cout<<lua_tostring(L,1)<<endl;	//转为string并返回
	}
	if( lua_isnumber(L,2)){
		cout<<lua_tonumber(L,2)<<endl;
	}
 
	//4.关闭state
	lua_close(L);
	return ;
}
```

### c++调用lua
堆栈操作是基于栈顶的，就是说它只会去操作栈顶的值。  

函数调用流程是先将函数入栈，参数入栈，然后用lua_pcall调用函数，此时栈顶为参数，栈底为函数，所以栈过程大致会是：参数出栈->保存参数->参数出栈->保存参数->函数出栈->调用函数->返回结果入栈。  

类似的还有lua_setfield，设置一个表的值，肯定要先将值出栈，保存，再去找表的位置。  

### lua调用c++
#### 1. 直接将模块写入lua源码中
将函数写lua.c中，然后重新编译lua文件。

#### 2. 使用静态依赖的方式
在c++中写一个模块函数，将函数注册到lua解释器中，然后由c++去执行我们的lua文件，然后在lua中调用刚刚注册的函数。  

#### 3. 使用dll动态链接的方式
新建一个dll工程，然后编写c++模块。  

在Lua中通过`require "dllName"`来使用。
Lua会进行以下操作：  
```lua
local path = "dllName.dll"
local f = package.loadlib(path,"luaopen_dllName") -- -- 返回luaopen_dllName函数
f() -- 执行函数
```

### 总结
  * c++调用lua实际上是：由c++先把数据放入栈中，由lua去栈中取数据，然后返回数据对应的值到栈顶，再由栈顶返回c++。
  * lua调c++也一样：先编写自己的c模块，然后注册函数到lua解释器中，然后由lua去调用这个模块的函数。