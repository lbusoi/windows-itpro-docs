---
title: Policy CSP - RestrictedGroups
description: Policy CSP - RestrictedGroups
ms.author: dansimp
ms.topic: article
ms.prod: w10
ms.technology: windows
author: manikadhiman
ms.localizationpriority: medium
ms.date: 03/24/2020

ms.reviewer: 
manager: dansimp
---

# Policy CSP - RestrictedGroups



<hr/>

<!--Policies-->
## RestrictedGroups policies  

<dl>
  <dd>
    <a href="#restrictedgroups-configuregroupmembership">RestrictedGroups/ConfigureGroupMembership</a>
  </dd>
</dl>


<hr/>

<!--Policy-->
<a href="" id="restrictedgroups-configuregroupmembership"></a>**RestrictedGroups/ConfigureGroupMembership**  

<!--SupportedSKUs-->
<table>
<tr>
    <th>Windows Edition</th>
    <th>Supported?</th>
</tr>
<tr>
    <td>Home</td>
    <td><img src="images/crossmark.png" alt="cross mark" /></td>
</tr>
<tr>
    <td>Pro</td>
    <td><img src="images/checkmark.png" alt="check mark" /><sup>4</sup></td>
</tr>
<tr>
    <td>Business</td>
    <td><img src="images/checkmark.png" alt="check mark" /><sup>4</sup></td>
</tr>
<tr>
    <td>Enterprise</td>
    <td><img src="images/checkmark.png" alt="check mark" /><sup>4</sup></td>
</tr>
<tr>
    <td>Education</td>
    <td><img src="images/checkmark.png" alt="check mark" /><sup>4</sup></td>
</tr>
</table>

<!--/SupportedSKUs-->
<hr/>

<!--Scope-->
[Scope](./policy-configuration-service-provider.md#policy-scope):

> [!div class = "checklist"]
> * Device

<hr/>

<!--/Scope-->
<!--Description-->
This security setting allows an administrator to define the members that are part of a security-sensitive (restricted) group. When a Restricted Groups policy is enforced, any current member of a restricted group that is not on the Members list is removed, except for the built-in administrator in the built-in Administrators group. Any user on the Members list who is not currently a member of the restricted group is added. An empty Members list means that the restricted group has no members. The membership configuration is based on SIDS, therefore renaming these built-in groups does not affect retention of this special membership.

For example, you can create a Restricted Groups policy to allow only specified users, Alice and John, to be members of the Backup Operators group. When this policy is refreshed, only Alice and John will remain as members of the Backup Operators group and all other members will be removed.  

> [!CAUTION]
> Attempting to remove the built-in administrator from the Administrators group will result in failure with the following error:  
>
> | Error Code  | Symbolic Name | Error Description | Header |
> |----------|----------|----------|----------|
> |  0x55b (Hex)  <br>  1371 (Dec)  |ERROR_SPECIAL_ACCOUNT|Cannot perform this operation on built-in accounts.|  winerror.h  |

Starting in Windows 10, version 1809, you can use this schema for retrieval and application of the RestrictedGroups/ConfigureGroupMembership policy. A minimum occurrence of 0 members when applying the policy implies clearing the access group and should be used with caution.

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" version="1.0">  
  <xs:simpleType name="member_name">
    <xs:restriction base="xs:string">
      <xs:maxLength value="255" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="accessgroup">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="member" minOccurs="0" maxOccurs="unbounded">
          <xs:annotation>
            <xs:documentation>Restricted Group Member</xs:documentation>
          </xs:annotation>
          <xs:complexType>
           <xs:attribute name="name" type="member_name" use="required"/>
          </xs:complexType>
        </xs:element>
      </xs:sequence>
      <xs:attribute name="desc" type="member_name" use="required"/>
    </xs:complexType>
  </xs:element>
  <xs:element name="groupmembership">
    <xs:complexType>
       <xs:sequence>
          <xs:element name="accessgroup" minOccurs="0" maxOccurs="unbounded">
           <xs:annotation>
              <xs:documentation>Restricted Group</xs:documentation>
            </xs:annotation>
          </xs:element>
       </xs:sequence>
    </xs:complexType>
   </xs:element>
</xs:schema>
```

<!--/Description-->
<!--SupportedValues-->

<!--/SupportedValues-->
<!--Example-->

Here's an example:
```
<groupmembership>
    <accessgroup desc = "Group1">
        <member name = "S-1-15-6666767-76767676767-666666777"/>
        <member name = "contoso\Alice"/>
    </accessgroup>
    <accessgroup desc = "Group2">
        <member name = "S-1-15-1233433-23423432423-234234324"/>
        <member name = "Group1"/>
    </accessgroup>
</groupmembership>
```
where:
- `<accessgroup desc>` contains the local group SID or group name to configure. If an SID is specified here, the policy uses the [LookupAccountName](https://docs.microsoft.com/windows/win32/api/winbase/nf-winbase-lookupaccountnamea) API to get the local group name. For best results, use names for `<accessgroup desc>`.
- `<member name>` contains the members to add to the group in `<accessgroup desc>`. If a name is specified here, the policy will try to get the corresponding SID using the [LookupAccountSID](https://docs.microsoft.com/windows/win32/api/winbase/nf-winbase-lookupaccountsida) API. (**Note:** This doesn't query Azure AD). For best results, use SID for `<member name>`. As groups can be renamed and account name lookups are limited to AD/local machine, hence SID is the best and most deterministic way to configure.
The member SID can be a user account or a group in AD, Azure AD, or on the local machine. Membership is configured using the [NetLocalGroupSetMembers](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netlocalgroupsetmembers) API.
- In this example, `Group1` and `Group2` are local groups on the device being configured.

<!--/Example-->
<!--Validation-->

<!--/Validation-->
<!--/Policy-->
<hr/>

Footnotes:

-   1 - Added in Windows 10, version 1607.
-   2 - Added in Windows 10, version 1703.
-   3 - Added in Windows 10, version 1709.
-   4 - Added in Windows 10, version 1803.
-   5 - Added in Windows 10, version 1809.
-   6 - Added in Windows 10, version 1903.

<!--/Policies-->
