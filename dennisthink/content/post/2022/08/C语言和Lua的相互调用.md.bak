---
title: "C语言和Lua的相互调用示例代码"
date: 2022-08-17T21:38:52+08:00
lastmod: 2022-08-17T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["economic"]
comment: true
mathjax: false
---


# C语言和Lua的相互调用示例代码

本代码参考博客: https://lucasklassmann.com/blog/2019-02-02-how-to-embeddeding-lua-in-c/


```c
/**
 * @file luaLearn.c
 * @author DennisThink (dennismi1024@gmail.com) (https://www.dennisthink.com/)
 * @brief C and Lua call Learn Demo
 * @version 0.1
 * @date 2022-08-19
 * 
 * @copyright Copyright (c) 2022
 * 
 */

//参考文档
//https://lucasklassmann.com/blog/2019-02-02-how-to-embeddeding-lua-in-c/

#include <stdio.h>
#include <lua.h>
#include <lualib.h>
#include <lauxlib.h>


//基本的Lua和C交互的函数
int BasicLuaFunction()
{
    lua_State* L = luaL_newstate();
    luaL_openlibs(L);
    // Work with lua API
    lua_close(L);
    return 0;
}


//无参数调用Lua
int CallLuaFunctionWithOutArguments()
{
    lua_State* L = luaL_newstate();
    luaL_openlibs(L);
    char* pLua = "print('this is lua')";
    // Work with lua API
     // Here we load the string and use lua_pcall for run the code
    int nLoadRet = luaL_loadstring(L, pLua);
    if (nLoadRet == LUA_OK) {
        if (lua_pcall(L, 0, 0, 0) == LUA_OK) {
            // If it was executed successfuly we 
            // remove the code from the stack
            lua_pop(L, lua_gettop(L));
        }
    }
    lua_close(L);
    return 0;
}

//Set Lua variable value in C function
int SetLuaVariableValueInCfunction()
{
    lua_State* L = luaL_newstate();
    luaL_openlibs(L);

    lua_pushinteger(L, 42);
    lua_setglobal(L, "answer");

    char* code = "print(answer)";

    if (luaL_dostring(L, code) == LUA_OK) {
        lua_pop(L, lua_gettop(L));
    }

    lua_close(L);
    return 0;
}

// Define our function, we have to follow the protocol of lua_CFunction that is 
// typedef int (*lua_CFunction) (lua_State *L);
// When this function is called by Lua, the stack contains the arguments needed, 
// what we need to do check if the arguments have the type that we expect.
int multiplication(lua_State* L) {

    // Check if the first argument is integer and return the value
    int a = luaL_checkinteger(L, 1);

    // Check if the second argument is integer and return the value
    int b = luaL_checkinteger(L, 2);

    // multiply and store the result inside a type lua_Integer
    lua_Integer c = a * b;

    // Here we prepare the values to be returned.
    // First we push the values we want to return onto the stack in direct order.
    // Second, we must return the number of values pushed onto the stack.

    // Pushing the result onto the stack to be returned
    lua_pushinteger(L, c);

    return 1; // The number of returned values
}

//从Lua中调用C语言编写的函数,带参数
int CallCfunctionFromLua()
{
    lua_State* L = luaL_newstate();
    luaL_openlibs(L);

    // Push the pointer to function
    lua_pushcfunction(L, multiplication);

    // Get the value on top of the stack
    // and set as a global, in this case is the function
    lua_setglobal(L, "mul");

    // we can use the function `mul` inside the Lua code
    char* code = "print(mul(7, 8))";

    if (luaL_dostring(L, code) == LUA_OK) {
        lua_pop(L, lua_gettop(L));
    }

    lua_close(L);
    return 0;

}


//func5.lua--> print("hello,word from lua")
//Call Lua function from lua file
int CallLuaFunctionFromLuaFile()
{
    lua_State* L = luaL_newstate();
    luaL_openlibs(L);

    if (luaL_dofile(L, "luafunction.lua") == LUA_OK) {
        lua_pop(L, lua_gettop(L));
    }

    lua_close(L);
    return 0;

}

//GetVariableValueFromLua.lua --->message = 'This message is stored inside lua code'
//从Lua文件中获取Lua变量的值
int GetVariableValueFromLuaFile()
{
    lua_State* L = luaL_newstate();
    luaL_openlibs(L);

    if (luaL_dofile(L, "GetVariableValueFromLua.lua") == LUA_OK) {
        lua_pop(L, lua_gettop(L));
    }

    lua_getglobal(L, "message");

    if (lua_isstring(L, -1)) {
        const char * message = lua_tostring(L, -1);
        lua_pop(L, 1);
        printf("Message from lua: %s\n", message);
    }

    lua_close(L);
    return 0;
}


//luafunc.lua --->
/*
function my_function()
    print("Hello from Function in Lua")
end
*/
int CallFunctionDefinedInLuaFile()
{
    lua_State *L = luaL_newstate();
    luaL_openlibs(L);

    if (luaL_dofile(L, "LuaDefinedFunction.lua") == LUA_OK) {
        lua_pop(L, lua_gettop(L));
    }

    lua_getglobal(L, "my_function");
    if (lua_isfunction(L, -1)) {
        if (lua_pcall(L, 0, 1, 0) == LUA_OK) {
            lua_pop(L, lua_gettop(L));
        }
    }

    lua_close(L);
    return 0;
}


//Calling a Lua function in C with arguments and return value
//luaFunctionWithArguments.lua
/*
function my_function(a, b)
    return a * b
end
*/

int CallFunctionDefinedInLuaFileWithArgmentsAndReturnValue()
{
     lua_State *L = luaL_newstate();
    luaL_openlibs(L);

    if (luaL_dofile(L, "luaFunctionWithArguments.lua") == LUA_OK) {
        lua_pop(L, lua_gettop(L));
    }

    // Put the function to be called onto the stack
    lua_getglobal(L, "my_function");
    lua_pushinteger(L, 3);  // first argument
    lua_pushinteger(L, 4);  // second argument

    // Execute my_function with 2 arguments and 1 return value
    if (lua_pcall(L, 2, 1, 0) == LUA_OK) {

        // Check if the return is an integer
        if (lua_isinteger(L, -1)) {

            // Convert the return value to integer
            int result = lua_tointeger(L, -1);

            // Pop the return value
            lua_pop(L, 1);
            printf("Result: %d\n", result);
        }
        // Remove the function from the stack
        lua_pop(L, lua_gettop(L));
    }

    lua_close(L);
    return 0;
}


int CallLuaFunctionWithErrorHandling()
{

    lua_State *L = luaL_newstate();
    luaL_openlibs(L);

    char * code = "print(return)"; // intentional error

    if (luaL_dostring(L, code) != LUA_OK) {
        puts(lua_tostring(L, lua_gettop(L)));
        lua_pop(L, lua_gettop(L));
    }

    lua_close(L);
    return 0;
}
int main(int argc, char** argv) {

    printf("This is c main function\n");
    BasicLuaFunction();
    CallLuaFunctionWithOutArguments();
    CallCfunctionFromLua();
    CallLuaFunctionFromLuaFile();
    CallFunctionDefinedInLuaFile();
    CallFunctionDefinedInLuaFileWithArgmentsAndReturnValue();
    CallLuaFunctionWithErrorHandling();
    SetLuaVariableValueInCfunction();
    GetVariableValueFromLuaFile();
    return 0;
}
/*int main(int argc, char* argv[])
{
	printf("This is c main function\n");
	return 0;
}*/

```