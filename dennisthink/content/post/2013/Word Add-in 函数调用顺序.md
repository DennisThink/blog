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
 //      If you are not running WinNT4.0 or Win95 with DCOM, then you
 //      need to remove the following define from dlldatax.c
 //      #define _WIN32_WINNT 0x0400
 //
 //      Further, if you are running MIDL without /Oicf switch, you also 
 //      need to remove the following define from dlldatax.c.
 //      #define USE_STUBLESS_PROXY
 //
 //      Modify the custom build rule for MyAddin.idl by adding the following 
 //      files to the Outputs.
 20 //          MyAddin_p.c
 //          dlldata.c
 //      To build a separate proxy/stub DLL, 
 //      run nmake -f MyAddinps.mk in the project directory.
 
 #include "stdafx.h"
 #include "resource.h"
 #include <initguid.h>
 #include "MyAddin.h"
 #include "dlldatax.h"
 
 #include "MyAddin_i.c"
 #include "WordAddin.h"
 
 #ifdef _MERGE_PROXYSTUB
 extern "C" HINSTANCE hProxyDll;
 #endif
 
 CComModule _Module;
 
 BEGIN_OBJECT_MAP(ObjectMap)
 OBJECT_ENTRY(CLSID_WordAddin, CWordAddin)
 END_OBJECT_MAP()
 
 /////////////////////////////////////////////////////////////////////////////
 // DLL Entry Point
 
 extern "C"
 BOOL WINAPI DllMain(HINSTANCE hInstance, DWORD dwReason, LPVOID lpReserved)
 {
         MessageBox(NULL,"DllMain",NULL,MB_OK);
     lpReserved;
 #ifdef _MERGE_PROXYSTUB
     if (!PrxDllMain(hInstance, dwReason, lpReserved))
         return FALSE;
 #endif
     if (dwReason == DLL_PROCESS_ATTACH)
     {
         _Module.Init(ObjectMap, hInstance, &LIBID_MYADDINLib);
         DisableThreadLibraryCalls(hInstance);
     }
     else if (dwReason == DLL_PROCESS_DETACH)
         _Module.Term();
     return TRUE;    // ok
 }
 
 /////////////////////////////////////////////////////////////////////////////
 // Used to determine whether the DLL can be unloaded by OLE
 
 STDAPI DllCanUnloadNow(void)
 {
     MessageBox(NULL,"DllCanUnloadNow",NULL,MB_OK);
 #ifdef _MERGE_PROXYSTUB
     if (PrxDllCanUnloadNow() != S_OK)
         return S_FALSE;
 #endif
     return (_Module.GetLockCount()==0) ? S_OK : S_FALSE;
 }
 
 /////////////////////////////////////////////////////////////////////////////
 // Returns a class factory to create an object of the requested type
 
 STDAPI DllGetClassObject(REFCLSID rclsid, REFIID riid, LPVOID* ppv)
 {
         MessageBox(NULL,"DllGetClassObject",NULL,MB_OK);
 #ifdef _MERGE_PROXYSTUB
     if (PrxDllGetClassObject(rclsid, riid, ppv) == S_OK)
         return S_OK;
 #endif
 
     return _Module.GetClassObject(rclsid, riid, ppv);
 }
 
 /////////////////////////////////////////////////////////////////////////////
 // DllRegisterServer - Adds entries to the system registry
 
 STDAPI DllRegisterServer(void)
 {
     MessageBox(NULL,"DllRegisterServer",NULL,MB_OK);
 #ifdef _MERGE_PROXYSTUB
   HRESULT hRes = PrxDllRegisterServer();
    if (FAILED(hRes))
        return hRes;
#endif
    // registers object, typelib and all interfaces in typelib
    
    return _Module.RegisterServer(TRUE);
}

/////////////////////////////////////////////////////////////////////////////
// DllUnregisterServer - Removes entries from the system registry

STDAPI DllUnregisterServer(void)
{
    MessageBox(NULL,"DllUnregisterServer",NULL,MB_OK);
#ifdef _MERGE_PROXYSTUB
    PrxDllUnregisterServer();
#endif

    return _Module.UnregisterServer(TRUE);
}
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
        ::CloseClipboard();
        ::DeleteObject(hBmp);        
        
        
        spCmdButton->PutStyle(Office::msoButtonIconAndCaption);
        hr = spCmdButton->PasteFace();
        if (FAILED(hr))
            return hr;
        
        spCmdButton->PutVisible(VARIANT_TRUE); 
        spCmdButton->PutCaption(OLESTR("myAddin")); 
        spCmdButton->PutEnabled(VARIANT_TRUE);
        spCmdButton->PutTooltipText(OLESTR("test1")); 
        spCmdButton->PutTag(OLESTR("test1")); 
        
        spNewCmdBar->PutVisible(VARIANT_TRUE); 
        
        m_spCmdButton = spCmdButton;

        CommandButton1Events::DispEventAdvise((IDispatch*)m_spCmdButton);
        return S_OK;
    }
    STDMETHOD(OnDisconnection)(ext_DisconnectMode RemoveMode, SAFEARRAY * * custom)
    {
        MessageBox(NULL,"OnDisconnection",NULL,MB_OK);
        if(m_spCmdButton != NULL)
            CommandButton1Events::DispEventUnadvise((IDispatch*)m_spCmdButton);
        return S_OK;
    }
    STDMETHOD(OnAddInsUpdate)(SAFEARRAY * * custom)
    {
        MessageBox(NULL,"OnAddInsUpdate",NULL,MB_OK);
        return S_OK;
    }
    STDMETHOD(OnStartupComplete)(SAFEARRAY * * custom)
    {
        MessageBox(NULL,"OnStartupComplete",NULL,MB_OK);
        return S_OK;
    }
    STDMETHOD(OnBeginShutdown)(SAFEARRAY * * custom)
    {
        MessageBox(NULL,"OnBeginShutdown",NULL,MB_OK);
        return S_OK;
    }

    void __stdcall OnClickButton1(IDispatch * /*Office::_CommandBarButton**/ Ctrl,VARIANT_BOOL * CancelDefault);

    // IRibbonExtensibility
    STDMETHOD(raw_GetCustomUI)(BSTR RibbonID, BSTR * RibbonXml)
    {
        MessageBox(NULL,"raw_GetCustomUI",NULL,MB_OK);
        OutputDebugString("raw_GetCustomUI");
        char szRibbon[MAX_PATH*10] = {0x00};
        LoadString(_Module.GetModuleInstance(),IDS_RIBBON_XML, \
            szRibbon, MAX_PATH*10);
        if (RibbonXml == NULL)
            return E_POINTER;

        OutputDebugString(szRibbon);
        
        CComBSTR bstr(szRibbon);
        bstr.CopyTo(RibbonXml);//use this to avoid heap destroy
        bstr.Empty();
        return S_OK;
    }
};

#endif //__WORDADDIN_H_
这是两个主要的文件。

注意两个问题：

        CComBSTR bstr(szRibbon);
        bstr.CopyTo(RibbonXml);//use this to avoid heap destroy
        bstr.Empty();
这三行代码是为了防止程序退出时，插件对word的内存进行修改，导致堆错误，Word会Crash。函数的含义请参考MSDN。

```