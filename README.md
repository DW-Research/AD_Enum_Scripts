![](W4key.gif)

# AD_Enum_Scripts
Scripts to enumerate AD Devies, Domain Admins, Windows 10 Machines

Domain Admin Finder 

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry
$Searcher.SearchRoot = $objDomain
$Searcher.filter="(name=*Domain Admins*)"
$Result = $Searcher.FindAll()
Foreach($obj in $Result)
{
	Foreach($prop in $obj.Properties)
	{
	$prop
	}
	Write-Host "------------------------"
}
```

#Device Enumeration
```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry
$Searcher.SearchRoot = $objDomain
$Searcher.filter="(objectClass=computer)"
$Result = $Searcher.FindAll()
Foreach($obj in $Result)
{
	Foreach($prop in $obj.Properties)
	{
	$prop
	}
	Write-Host "------------------------"
}
```

#Windows 10 Device Location

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

$PDC = ($domainObj.PdcRoleOwner).Name

$SearchString = "LDAP://"

$SearchString += $PDC + "/"

$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"

$SearchString += $DistinguishedName

$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)

$objDomain = New-Object System.DirectoryServices.DirectoryEntry($SearchString)

$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$objDomain)`

$Searcher.filter="objectClass=Computer"

$Result = $Searcher.FindAll()

Foreach($obj in $Result)
{
    Foreach($prop in $obj.Properties)
    {
    $prop
    }

Write-Host "------------------------"
}
```
Automated Nested Group Finder

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry
$Searcher.SearchRoot = $objDomain
$Searcher.filter="(objectClass=Group)"
$Result1 = $Searcher.FindAll()
Foreach($obj in $Result1)
{
	$Result2 = $obj.Properties.name
	Foreach($obj2 in $Result2)
	{
		$String = "`r`n"
		$String
		$obj2
		$Searcher.filter="(name=$obj2)"
		$Result3 = $Searcher.FindAll()
		Foreach($obj3 in $Result3)
		{
			$obj3.Properties.member
		}	
	}
}	
```

Enumerate SPN's

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry
$Searcher.SearchRoot = $objDomain
$Searcher.filter="serviceprincipalname=*http*"
$Result = $Searcher.FindAll()
Foreach($obj in $Result)
{
	Foreach($prop in $obj.Properties)
	{
	$prop
	}
}
```

Enumerate IP's

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry
$Searcher.SearchRoot = $objDomain

$Searcher.filter="serviceprincipalname=*"

$Result = $Searcher.FindAll()

Foreach($obj in $Result) {
    Foreach($prop in $obj.Properties) {
        $prop
        $SPNDomain = ([regex]::Matches($prop.serviceprincipalname, '([a-zA-Z0-9.-]+).[a-zA-Z0-9.-]+').value)
        if ($SPNDomain -ne $null) {
            $dnshost = $SPNDomain.split('/')[1].split(' ')
            "Domain: " + $dnshost
           $ip = [System.Net.Dns]::GetHostAddresses($dnshost)

            Foreach($address in $ip) {
                Write-Host("IP: " + $address)
                }
     }

    }
    Write-Host("========================================")
}
```


