---
title: "Word Add-in 函数调用顺序"
date: 2013-12-06T21:38:52+08:00
lastmod: 2013-12-06T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-cnblogs"]
comment: true
mathjax: false
---



这个图表明的函数的调用顺序，主要代码如下：
![](https://images0.cnblogs.com/blog/551396/201312/06174031-0a07914b91ca4c879fd40ba1abfef268.jpg)
```cpp
  1 // MyAddin.cpp : Implementation of DLL Exports.
  2 
  3 
  4 // Note: Proxy/Stub Information
  5 //      To merge the proxy/stub code into the object DLL, add the file 
  6 //      dlldatax.c to the project.  Make sure precompiled headers 
  7 //      are turned off for this file, and add _MERGE_PROXYSTUB to the 
  8 //      defines for the project.  
  9 //
 10 //      If you are not running WinNT4.0 or Win95 with DCOM, then you
 11 //      need to remove the following define from dlldatax.c
 12 //      #define _WIN32_WINNT 0x0400
 13 //
 14 //      Further, if you are running MIDL without /Oicf switch, you also 
 15 //      need to remove the following define from dlldatax.c.
 16 //      #define USE_STUBLESS_PROXY
 17 //
 18 //      Modify the custom build rule for MyAddin.idl by adding the following 
 19 //      files to the Outputs.
 20 //          MyAddin_p.c
 21 //          dlldata.c
 22 //      To build a separate proxy/stub DLL, 
 23 //      run nmake -f MyAddinps.mk in the project directory.
 24 
 25 #include "stdafx.h"
 26 #include "resource.h"
 27 #include <initguid.h>
 28 #include "MyAddin.h"
 29 #include "dlldatax.h"
 30 
 31 #include "MyAddin_i.c"
 32 #include "WordAddin.h"
 33 
 34 #ifdef _MERGE_PROXYSTUB
 35 extern "C" HINSTANCE hProxyDll;
 36 #endif
 37 
 38 CComModule _Module;
 39 
 40 BEGIN_OBJECT_MAP(ObjectMap)
 41 OBJECT_ENTRY(CLSID_WordAddin, CWordAddin)
 42 END_OBJECT_MAP()
 43 
 44 /////////////////////////////////////////////////////////////////////////////
 45 // DLL Entry Point
 46 
 47 extern "C"
 48 BOOL WINAPI DllMain(HINSTANCE hInstance, DWORD dwReason, LPVOID lpReserved)
 49 {
 50         MessageBox(NULL,"DllMain",NULL,MB_OK);
 51     lpReserved;
 52 #ifdef _MERGE_PROXYSTUB
 53     if (!PrxDllMain(hInstance, dwReason, lpReserved))
 54         return FALSE;
 55 #endif
 56     if (dwReason == DLL_PROCESS_ATTACH)
 57     {
 58         _Module.Init(ObjectMap, hInstance, &LIBID_MYADDINLib);
 59         DisableThreadLibraryCalls(hInstance);
 60     }
 61     else if (dwReason == DLL_PROCESS_DETACH)
 62         _Module.Term();
 63     return TRUE;    // ok
 64 }
 65 
 66 /////////////////////////////////////////////////////////////////////////////
 67 // Used to determine whether the DLL can be unloaded by OLE
 68 
 69 STDAPI DllCanUnloadNow(void)
 70 {
 71     MessageBox(NULL,"DllCanUnloadNow",NULL,MB_OK);
 72 #ifdef _MERGE_PROXYSTUB
 73     if (PrxDllCanUnloadNow() != S_OK)
 74         return S_FALSE;
 75 #endif
 76     return (_Module.GetLockCount()==0) ? S_OK : S_FALSE;
 77 }
 78 
 79 /////////////////////////////////////////////////////////////////////////////
 80 // Returns a class factory to create an object of the requested type
 81 
 82 STDAPI DllGetClassObject(REFCLSID rclsid, REFIID riid, LPVOID* ppv)
 83 {
 84         MessageBox(NULL,"DllGetClassObject",NULL,MB_OK);
 85 #ifdef _MERGE_PROXYSTUB
 86     if (PrxDllGetClassObject(rclsid, riid, ppv) == S_OK)
 87         return S_OK;
 88 #endif
 89 
 90     return _Module.GetClassObject(rclsid, riid, ppv);
 91 }
 92 
 93 /////////////////////////////////////////////////////////////////////////////
 94 // DllRegisterServer - Adds entries to the system registry
 95 
 96 STDAPI DllRegisterServer(void)
 97 {
 98     MessageBox(NULL,"DllRegisterServer",NULL,MB_OK);
 99 #ifdef _MERGE_PROXYSTUB
100     HRESULT hRes = PrxDllRegisterServer();
101     if (FAILED(hRes))
102         return hRes;
103 #endif
104     // registers object, typelib and all interfaces in typelib
105     
106     return _Module.RegisterServer(TRUE);
107 }
108 
109 /////////////////////////////////////////////////////////////////////////////
110 // DllUnregisterServer - Removes entries from the system registry
111 
112 STDAPI DllUnregisterServer(void)
113 {
114     MessageBox(NULL,"DllUnregisterServer",NULL,MB_OK);
115 #ifdef _MERGE_PROXYSTUB
116     PrxDllUnregisterServer();
117 #endif
118 
119     return _Module.UnregisterServer(TRUE);
120 }
还有一份代码如下：

  1 // WordAddin.h : Declaration of the CWordAddin
  2 
  3 #ifndef __WORDADDIN_H_
  4 #define __WORDADDIN_H_
  5 
  6 #include "resource.h"       // main symbols
  7 #include "stdafx.h"
  8 #include "StdAfx.h"
  9 #import "C:\Program Files\Common Files\designer\MSADDNDR.dll" raw_interfaces_only, raw_native_types, no_namespace, named_guids 
 10 
 11 extern _ATL_FUNC_INFO OnClickButtonInfo;
 12 
 13 /////////////////////////////////////////////////////////////////////////////
 14 // CWordAddin
 15 class ATL_NO_VTABLE CWordAddin : 
 16     public CComObjectRootEx<CComSingleThreadModel>,
 17     public CComCoClass<CWordAddin, &CLSID_WordAddin>,
 18     public IDispatchImpl<IWordAddin, &IID_IWordAddin, &LIBID_MYADDINLib>,
 19     public IDispatchImpl<_IDTExtensibility2, &IID__IDTExtensibility2, &LIBID_AddInDesignerObjects>,
 20     public IDispEventSimpleImpl<1,CWordAddin,&__uuidof(Office::_CommandBarButtonEvents)>,
 21     public IDispatchImpl<IRibbonExtensibility, &IID_IRibbonExtensibility, &LIBID_Office>
 22 {
 23 public:
 24     CWordAddin()
 25     {
 26         int i = 0;
 27     }
 28 
 29 DECLARE_REGISTRY_RESOURCEID(IDR_WORDADDIN)
 30 
 31 DECLARE_PROTECT_FINAL_CONSTRUCT()
 32 
 33 BEGIN_COM_MAP(CWordAddin)
 34     COM_INTERFACE_ENTRY(IWordAddin)
 35 //DEL     COM_INTERFACE_ENTRY(IDispatch)
 36     COM_INTERFACE_ENTRY2(IDispatch, IWordAddin)
 37     COM_INTERFACE_ENTRY(_IDTExtensibility2)
 38     COM_INTERFACE_ENTRY(IRibbonExtensibility)
 39 END_COM_MAP()
 40 
 41 BEGIN_SINK_MAP(CWordAddin)
 42 SINK_ENTRY_INFO(1, __uuidof(Office::_CommandBarButtonEvents),/*dispid*/ 0x01, OnClickButton1, &OnClickButtonInfo)
 43 END_SINK_MAP()
 44 
 45 // IWordAddin
 46 public:
 47     STDMETHOD(OnMyButton)(/*[in]*/IDispatch* pIDispControl);
 48     CComQIPtr <Word::_Application> m_spApp;
 49     CComPtr < Office::_CommandBarButton> m_spCmdButton;
 50     typedef IDispEventSimpleImpl</*nID =*/ 1,CWordAddin, &__uuidof(Office::_CommandBarButtonEvents)> CommandButton1Events;
 51 // _IDTExtensibility2
 52     STDMETHOD(OnConnection)(IDispatch * Application, ext_ConnectMode ConnectMode, IDispatch * AddInInst, SAFEARRAY * * custom)
 53     {
 54         ::MessageBox(NULL,"OnConnection","",MB_OK);
 55         OutputDebugString("OnConnection");
 56         //Delete it for the Office 2007
 57         
 58         CComPtr < Office::_CommandBars> spCmdBars; 
 59         CComQIPtr <Word::_Application> spApp(Application);     
 60         m_spApp = spApp;
 61         ATLASSERT(spApp);
 62 
 63         HRESULT hr = spApp->get_CommandBars(&spCmdBars);
 64         if(FAILED(hr))
 65             return hr;
 66             
 67         ATLASSERT(spCmdBars);
 68         
 69     
 70         CComVariant vName("MyAddin");
 71         CComPtr <Office::CommandBar> spNewCmdBar;
 72         CComVariant vPos(1); 
 73         CComVariant vTemp(VARIANT_TRUE);    
 74         CComVariant vEmpty(DISP_E_PARAMNOTFOUND, VT_ERROR);            
 75         spNewCmdBar = spCmdBars->Add(vName, vPos, vEmpty, vTemp);
 76         
 77         
 78         CComPtr < Office::CommandBarControls> spBarControls;
 79         spBarControls = spNewCmdBar->GetControls();
 80         ATLASSERT(spBarControls);
 81         
 82         
 83         CComVariant vToolBarType(1);
 84         CComVariant vShow(VARIANT_TRUE);
 85         CComPtr < Office::CommandBarControl> spNewBar; 
 86         spNewBar = spBarControls->Add(vToolBarType, vEmpty, vEmpty, vEmpty, vShow); 
 87         ATLASSERT(spNewBar);
 88         
 89             
 90         
 91         CComQIPtr < Office::_CommandBarButton> spCmdButton(spNewBar);
 92         ATLASSERT(spCmdButton);
 93         
 94         HBITMAP hBmp =(HBITMAP)::LoadImage(_Module.GetResourceInstance(),
 95             MAKEINTRESOURCE(IDB_BITMAP),IMAGE_BITMAP,0,0,LR_LOADMAP3DCOLORS);
 96         
 97         ::OpenClipboard(NULL);
 98         ::EmptyClipboard();
 99         ::SetClipboardData(CF_BITMAP, (HANDLE)hBmp);
100         ::CloseClipboard();
101         ::DeleteObject(hBmp);        
102         
103         
104         spCmdButton->PutStyle(Office::msoButtonIconAndCaption);
105         hr = spCmdButton->PasteFace();
106         if (FAILED(hr))
107             return hr;
108         
109         spCmdButton->PutVisible(VARIANT_TRUE); 
110         spCmdButton->PutCaption(OLESTR("myAddin")); 
111         spCmdButton->PutEnabled(VARIANT_TRUE);
112         spCmdButton->PutTooltipText(OLESTR("test1")); 
113         spCmdButton->PutTag(OLESTR("test1")); 
114         
115         spNewCmdBar->PutVisible(VARIANT_TRUE); 
116         
117         m_spCmdButton = spCmdButton;
118 
119         CommandButton1Events::DispEventAdvise((IDispatch*)m_spCmdButton);
120         return S_OK;
121     }
122     STDMETHOD(OnDisconnection)(ext_DisconnectMode RemoveMode, SAFEARRAY * * custom)
123     {
124         MessageBox(NULL,"OnDisconnection",NULL,MB_OK);
125         if(m_spCmdButton != NULL)
126             CommandButton1Events::DispEventUnadvise((IDispatch*)m_spCmdButton);
127         return S_OK;
128     }
129     STDMETHOD(OnAddInsUpdate)(SAFEARRAY * * custom)
130     {
131         MessageBox(NULL,"OnAddInsUpdate",NULL,MB_OK);
132         return S_OK;
133     }
134     STDMETHOD(OnStartupComplete)(SAFEARRAY * * custom)
135     {
136         MessageBox(NULL,"OnStartupComplete",NULL,MB_OK);
137         return S_OK;
138     }
139     STDMETHOD(OnBeginShutdown)(SAFEARRAY * * custom)
140     {
141         MessageBox(NULL,"OnBeginShutdown",NULL,MB_OK);
142         return S_OK;
143     }
144 
145     void __stdcall OnClickButton1(IDispatch * /*Office::_CommandBarButton**/ Ctrl,VARIANT_BOOL * CancelDefault);
146 
147     // IRibbonExtensibility
148     STDMETHOD(raw_GetCustomUI)(BSTR RibbonID, BSTR * RibbonXml)
149     {
150         MessageBox(NULL,"raw_GetCustomUI",NULL,MB_OK);
151         OutputDebugString("raw_GetCustomUI");
152         char szRibbon[MAX_PATH*10] = {0x00};
153         LoadString(_Module.GetModuleInstance(),IDS_RIBBON_XML, \
154             szRibbon, MAX_PATH*10);
155         if (RibbonXml == NULL)
156             return E_POINTER;
157 
158         OutputDebugString(szRibbon);
159         
160         CComBSTR bstr(szRibbon);
161         bstr.CopyTo(RibbonXml);//use this to avoid heap destroy
162         bstr.Empty();
163         return S_OK;
164     }
165 };
166 
167 #endif //__WORDADDIN_H_
这是两个主要的文件。

注意两个问题：

160         CComBSTR bstr(szRibbon);
161         bstr.CopyTo(RibbonXml);//use this to avoid heap destroy
162         bstr.Empty();
这三行代码是为了防止程序退出时，插件对word的内存进行修改，导致堆错误，Word会Crash。函数的含义请参考MSDN。

```