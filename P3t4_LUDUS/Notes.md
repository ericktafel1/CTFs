- NTLM hashes at start
```
58a478135a93ac3bf058a5ea0e8fdb71 --- Password123
a82e5f767686715361a379946263872e
4d7208988cc83aa2cc846d90ec880036 --- flowergirl
e0b33993edcd7aeb4959da757ba102f3
4b278de09ba28c8e878410224a59a052
c7acc70d0df8f2a4ba84cd42dbc2040e
cf3a5525ee9414229e66279623ed5c58 --- Welcome1
a72caf882e7acccfaacf2eedd14f5a96
e18ecb9a1f8974a87985cf502128943e
e77eb818f2ba9f345bd879223c4869d2
```

- ports
```
Open 10.4.10.100:53
Open 10.4.10.100:88
Open 10.4.10.100:135
Open 10.4.10.100:139
Open 10.4.10.100:389
Open 10.4.10.100:445
Open 10.4.10.100:464
Open 10.4.10.100:593
Open 10.4.10.100:636
Open 10.4.10.100:3268
Open 10.4.10.100:3269
Open 10.4.10.100:3389
Open 10.4.10.100:5986
Open 10.4.10.100:9389
Open 10.4.10.100:49664
Open 10.4.10.100:49668
Open 10.4.10.100:60206
Open 10.4.10.100:60205
Open 10.4.10.100:60222
Open 10.4.10.100:60240
```

- DC = `GAIA.DC01`
- Description has username `Tifa` who setup AD
![[Pasted image 20260119163716.png]]
- Guest SMB with `Tifa : Password123`
![[Pasted image 20260119164206.png]]
- SMBGhost
- Enum shares
- `turks`
![[Pasted image 20260119164911.png]]
- `turks_net`
![[Pasted image 20260119165000.png]]
- cant open the files and no meta data
	- just headers in file.