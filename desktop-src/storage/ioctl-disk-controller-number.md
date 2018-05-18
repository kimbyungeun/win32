---
title: IOCTL\_DISK\_CONTROLLER\_NUMBER control code
description: Retrieves the controller number and disk number for an IDE disk.
ms.assetid: 'dbf7829b-c5b9-4428-a296-34199a726ec5'
keywords: ["IOCTL_DISK_CONTROLLER_NUMBER control code Storage Devices"]
topic_type:
- apiref
api_name:
- IOCTL_DISK_CONTROLLER_NUMBER
api_location:
- Ntdddisk.h
api_type:
- HeaderDef
---

# IOCTL\_DISK\_CONTROLLER\_NUMBER control code

Retrieves the controller number and disk number for an IDE disk.

## Input Buffer

None.

## Input Buffer Length

None.

## Output Buffer

The buffer at **Irp-&gt;AssociatedIrp.SystemBuffer** contains the [**DISK\_CONTROLLER\_NUMBER**](disk-controller-number.md) data.

## Output Buffer Length

**Parameters.DeviceIoControl.OutputBufferLength** in the IO\_STACK\_LOCATION structure of the IRP indicates the size, in bytes, of the output buffer, which must be &gt;= **sizeof**(DISK\_CONTROLLER\_NUMBER).

## Status block

The **Information** field is set to **sizeof**(DISK\_CONTROLLER\_NUMBER).

The **Status** field is set to STATUS\_SUCCESS if the operation is successful. One possible status value is STATUS\_BUFFER\_TOO\_SMALL if the output buffer provided by the caller is too small.

## Requirements



|                   |                                                                                                            |
|-------------------|------------------------------------------------------------------------------------------------------------|
| Header<br/> | <dl> <dt>Ntdddisk.h (include Ntdddisk.h)</dt> </dl> |



## See also

<dl> <dt>

[**DISK\_CONTROLLER\_NUMBER**](disk-controller-number.md)
</dt> </dl>

�

�

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20%5Bstorage\storage%5D:%20IOCTL_DISK_CONTROLLER_NUMBER%20control%20code%20%20RELEASE:%20%283/29/2018%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")





