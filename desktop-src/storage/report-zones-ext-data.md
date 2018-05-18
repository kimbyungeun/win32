---
title: REPORT\_ZONES\_EXT\_DATA structure
description: Note This structure is for internal use only and should not be called from your code.�.
ms.assetid: '0c6b4b7c-548d-42c0-af9b-cf0d65bf2e45'
keywords: ["REPORT_ZONES_EXT_DATA structure Storage Devices", "PREPORT_ZONES_EXT_DATA structure pointer Storage Devices"]
topic_type:
- apiref
api_name:
- REPORT_ZONES_EXT_DATA
api_location:
- ata.h
api_type:
- HeaderDef
---

# REPORT\_ZONES\_EXT\_DATA structure

> [!Note]  
> This structure is for internal use only and should not be called from your code.

�

## Syntax


```C++
typedef struct _REPORT_ZONES_EXT_DATA {
  ULONG  ���ZoneListLength;
  UCHAR ����SAME  :4;
  UCHAR ����Reserved0  :4;
  UCHAR ����Reserved1[3];
  ULONGLONG MaxLBA  :48;
  ULONGLONG Reserved2  :16;
  UCHAR ����Reserved3[48];
} REPORT_ZONES_EXT_DATA, *PREPORT_ZONES_EXT_DATA;
```



## Members

<dl> <dt>

**ZoneListLength**
</dt> <dd>

N/A

</dd> <dt>

**SAME**
</dt> <dd>

N/A

</dd> <dt>

**Reserved0**
</dt> <dd>

Reserved for future use.

</dd> <dt>

**Reserved1**
</dt> <dd>

Reserved for future use.

</dd> <dt>

**MaxLBA**
</dt> <dd>

N/A

</dd> <dt>

**Reserved2**
</dt> <dd>

Reserved for future use.

</dd> <dt>

**Reserved3**
</dt> <dd>

Reserved for future use.

</dd> </dl>

## Requirements



|                   |                                                                                  |
|-------------------|----------------------------------------------------------------------------------|
| Header<br/> | <dl> <dt>Ata.h</dt> </dl> |



�

�

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20%5Bstorage\storage%5D:%20REPORT_ZONES_EXT_DATA%20structure%20%20RELEASE:%20%283/29/2018%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")




