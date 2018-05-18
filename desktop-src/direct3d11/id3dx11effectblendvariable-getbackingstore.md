---
title: ID3DX11EffectBlendVariable GetBackingStore method
description: Get a pointer to a blend-state variable.
ms.assetid: '809daaad-9bf0-48fb-ae91-f237c43db324'
keywords: ["GetBackingStore method Direct3D 11", "GetBackingStore method Direct3D 11 , ID3DX11EffectBlendVariable interface", "ID3DX11EffectBlendVariable interface Direct3D 11 , GetBackingStore method"]
topic_type:
- apiref
api_name:
- ID3DX11EffectBlendVariable.GetBackingStore
api_location:
- N/A
- N/A.dll
api_type:
- COM
---

# ID3DX11EffectBlendVariable::GetBackingStore method

Get a pointer to a blend-state variable.

## Syntax


```C++
HRESULT GetBackingStore(
  �UINT ������������Index,
  �D3D11_BLEND_DESC *pBlendDesc
);
```



## Parameters

<dl> <dt>

*Index* 
</dt> <dd>

Type: **[**UINT**](https://msdn.microsoft.com/library/windows/desktop/aa383751)**

Index into an array of blend-state descriptions. If there is only one blend-state variable in the effect, use 0.

</dd> <dt>

*pBlendDesc* 
</dt> <dd>

Type: **[**D3D11\_BLEND\_DESC**](d3d11-blend-desc.md)\***

A pointer to a blend-state description (see [**D3D11\_BLEND\_DESC**](d3d11-blend-desc.md)).

</dd> </dl>

## Return value

Type: **[**HRESULT**](455d07e9-52c3-4efb-a9dc-2955cbfd38cc)**

Returns one of the following [Direct3D 11 Return Codes](d3d11-graphics-reference-returnvalues.md).

## Remarks

Effect variables are saved in memory in the backing store; when a technique is applied, the values in the backing store are copied to the device. Backing store data can used to recreate the variable when necessary.

> [!Note]  
> The DirectX SDK does not supply any compiled binaries for effects. You must use Effects 11 source to build your effects-type application. For more information about using Effects 11 source, see [Differences Between Effects 10 and Effects 11](d3d11-graphics-programming-guide-effects-differences.md).

�

## Requirements



|                    |                                                                                                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| Header<br/>  | <dl> <dt>D3dx11effect.h</dt> </dl>                                                    |
| Library<br/> | <dl> <dt>N/A (An Effects 11 library is available online as shared source.)</dt> </dl> |



## See also

<dl> <dt>

[ID3DX11EffectBlendVariable](id3dx11effectblendvariable.md)
</dt> </dl>

�

�




