---
Description: Defining the Service Properties
title: Defining the Service Properties
ms.author: windowsdriverdev
ms.date: 04/20/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
---

# Defining the Service Properties


WPD properties are object-description metadata. This section describes the properties that the sample driver supports. The Contacts Service Object supports 14 properties; and the individual contact objects also support 14 properties. Some object properties are required for driver functionality, such as WPD\_OBJECT\_ID and WPD\_OBJECT\_PERSISTENT\_UNIQUE\_ID. Other properties provide information to describe the object, such as WPD\_CONTACT\_DISPLAY\_NAME for the contact objects.

The WDK includes several tools for WPD driver developers. One of these tools, *WpdInfo.exe*, enables a developer to examine the objects and properties that are exposed by a given driver. The following image of the *WpdInfo.exe* tool shows the properties that the Contacts Service Object in the driver supports:

![the wpd information tool](images/wpd_info_service_root.png)

In the previous image, the leftmost column in the top pane lists the objects that the driver supports. The center pane lists the 14 properties that the driver for the Contacts Service Object supports. The first column in this pane lists the property name, the second column lists the value of that property, and the third column lists the type, and so on. The lower pane shows information that is returned by the event that the driver supports.

The following image of the *WpdInfo.exe* tool shows the properties that the first contact object supports.

![the wpd information tool](images/wpd_info_service_contact1.png)

This object supports 14 properties. The first eight properties are general WPD object properties. The last six properties are contact-specific (name, mobile-phone number, email address, and so on).

In WPD, properties are represented by the PROPERTYKEY data structure. This structure consists of two parts: a globally unique identifier (GUID) and a DWORD. The GUID identifies a property category, and the DWORD identifies a particular property in that category. For more information about the PROPERTYKEY structure, see [PROPERTYKEYs and GUIDs in WPD](propertykeys-and-guids-in-windows-portable-devices.md).

In the sample driver, the supported contact properties are defined in an array of **PropertyAttributeInfo** structures. This structure has the following format:

```
typedef struct tagPropertyAttributeInfo
{
    const PROPERTYKEY*                pKey;
    VARTYPE                           Vartype;
    FakeDevicePropertyAttributesType  AttributesType;
    const LPWSTR                      wszName;
} PropertyAttributeInfo;
```

The *FakeContactContent.cpp* file defines the array of supported contact properties:

```
const PropertyAttributeInfo g_SupportedContactProperties[] =
{
    {&WPD_OBJECT_ID,                             VT_LPWSTR, UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  NULL}, 
    {&WPD_OBJECT_PERSISTENT_UNIQUE_ID,           VT_LPWSTR, UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  NULL}, 
    {&WPD_OBJECT_PARENT_ID,                      VT_LPWSTR, UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  NULL}, 
    {&WPD_OBJECT_NAME,                           VT_LPWSTR, UnspecifiedForm_CanRead_CanWrite_CannotDelete_Fast,     NULL}, 
    {&WPD_OBJECT_FORMAT,                         VT_CLSID,  UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  NULL}, 
    {&WPD_OBJECT_CONTENT_TYPE,                   VT_CLSID,  UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  NULL}, 
    {&WPD_OBJECT_CAN_DELETE,                     VT_BOOL,   UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  NULL}, 
    {&WPD_OBJECT_CONTAINER_FUNCTIONAL_OBJECT_ID, VT_LPWSTR, UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  NULL}, 
    {&WPD_CONTACT_DISPLAY_NAME,                  VT_LPWSTR, UnspecifiedForm_CanRead_CanWrite_CannotDelete_Fast,     NULL},
    {&WPD_CONTACT_MOBILE_PHONE,                  VT_LPWSTR, UnspecifiedForm_CanRead_CanWrite_CannotDelete_Fast,     NULL},
    {&WPD_CONTACT_PRIMARY_EMAIL_ADDRESS,         VT_LPWSTR, UnspecifiedForm_CanRead_CanWrite_CannotDelete_Fast,     NULL},
    {&WPD_CONTACT_PRIMARY_PHONE,                 VT_LPWSTR, UnspecifiedForm_CanRead_CanWrite_CannotDelete_Fast,     NULL},
    {&WPD_CONTACT_PERSONAL_FULL_POSTAL_ADDRESS,  VT_LPWSTR, UnspecifiedForm_CanRead_CanWrite_CannotDelete_Fast,     NULL},
    {&MyContactVersionIdentifier,                VT_UI4,    UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast,  L"ContactVersionIdentifier"},
};
```

These properties are initialized in the *FakeContactsServiceContent.cpp* module in the **InitializeContent** method, as shown in the following example:

```
        if (pContact)
        {
            pContact->Name.Format(L"Contact%d", *pdwLastObjectID);
            pContact->ParentID                    = ObjectID;
            pContact->ContainerFunctionalObjectID = ObjectID;
            pContact->ObjectID.Format(L"%d", *pdwLastObjectID);
            pContact->PersistentUniqueID.Format(L"PersistentUniqueID_%ws", pContact->ObjectID);
            pContact->ParentPersistentUniqueID    = PersistentUniqueID;
            pContact->RequiredScope               = CONTACTS_SERVICE_ACCESS;

            pContact->DisplayName.Format(L"Surname%d, FirstName%d", dwContactIndex, dwContactIndex);
            pContact->MobilePhoneNumber           = L"(425) 555 0821";
            pContact->PrimaryPhoneNumber          = L"(425) 556 6010";
            pContact->PrimaryEmail.Format(L"FirstName%d@Company%d.com", dwContactIndex, dwContactIndex);
            pContact->PersonalFullPostalAddress.Format(L"One Microsoft Way, Redmond, WA 98052, USA");

            m_Children.Add(pContact);
        }
```

The *FakeContactsServiceContent.cpp* file defines the array of supported service properties:

```
const PropertyAttributeInfo g_SupportedServiceProperties[] =
{
    {&WPD_OBJECT_ID,                                VT_LPWSTR,          UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_OBJECT_PERSISTENT_UNIQUE_ID,              VT_LPWSTR,          UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_OBJECT_PARENT_ID,                         VT_LPWSTR,          UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_OBJECT_NAME,                              VT_LPWSTR,          UnspecifiedForm_CanRead_CanWrite_CannotDelete_Fast,    NULL},
    {&WPD_OBJECT_FORMAT,                            VT_CLSID,           UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_OBJECT_CONTENT_TYPE,                      VT_CLSID,           UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_OBJECT_CAN_DELETE,                        VT_BOOL,            UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_OBJECT_CONTAINER_FUNCTIONAL_OBJECT_ID,    VT_LPWSTR,          UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_FUNCTIONAL_OBJECT_CATEGORY,               VT_CLSID,           UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&WPD_SERVICE_VERSION,                          VT_LPWSTR,          UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, NULL},
    {&PKEY_Services_ServiceDisplayName,             VT_LPWSTR,          UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, L"DisplayName"},
    {&PKEY_FullEnumSyncSvc_SyncFormat,              VT_CLSID,           UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, L"FullEnumSyncFormat"},
    {&PKEY_Services_ServiceIcon,                    VT_VECTOR | VT_UI1, UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, L"ServiceIcon"},
    {&PKEY_FullEnumSyncSvc_VersionProps,            VT_VECTOR | VT_UI1, UnspecifiedForm_CanRead_CannotWrite_CannotDelete_Fast, L"FullEnumVersionProps"}
};
```

The service properties are set in the **GetValue** method that is found in the same module.

## <span id="related_topics"></span>Related topics


****
[The WpdServiceSampleDriverSample](the-wpdservicesampledriver-sample.md)

[The WPD Driver Samples](the-wpd-driver-samples.md)

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20[wpd_dk\wpddk]:%20Defining%20the%20Service%20Properties%20%20RELEASE:%20%281/5/2017%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")




