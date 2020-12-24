## How to compare file sizes of files in two folders, to see if they are identical

In this case, let's compare the two:
- files named `file-007.wav` and so on, from one folder, 
- files named `01.Orphée_aux_enfers.wav` from another folder.    

We want to filter only these files, and not show other files named `output.wav` or other folders.    
Below is the file listing for the folders we want to compare.  

Folder 1  
```
[george@archie test]$ ls -lt
total 737276
-rw-r--r-- 1 george george  7961564 Dec 24 21:34 file-007.wav
-rw-r--r-- 1 george george 19622780 Dec 24 21:34 file-006.wav
-rw-r--r-- 1 george george  3210524 Dec 24 21:34 file-005.wav
-rw-r--r-- 1 george george 34762604 Dec 24 21:34 file-004.wav
-rw-r--r-- 1 george george  3838508 Dec 24 21:34 file-003.wav
-rw-r--r-- 1 george george  5840060 Dec 24 21:34 file-002.wav
-rw-r--r-- 1 george george 24390284 Dec 24 21:34 file-001.wav
```
Folder 2  
```
[george@archie test]$ ls -lt ..
total 761088
drwxr-xr-x 1 george george      818 Dec 24 22:21  test
-rw-r--r-- 1 george george 24390318 Dec 24 21:34  output.wav
-rw-r--r-- 1 george george  7961564 Dec 24 21:34 '07.Orphée_aux_enfers__Acte_deuxième._Dialogue_Silence,_ou_je_tonne..._(Jupiter,_Pluton,_Diane).wav'
-rw-r--r-- 1 george george 19622780 Dec 24 21:34 '06.Orphée_aux_enfers__Acte_deuxième._Chœur_de_la_Revolte_Aux_armes,_dieux_et_demi-dieux!_(Diane,_Vénus,_Cupidon,_Pluton,_Jupiter,_les_Dieux_et_les_Déesses).wav'
-rw-r--r-- 1 george george  3210524 Dec 24 21:34 '05.Orphée_aux_enfers__Acte_deuxième._Dialogue_Laissons_cela!_(Jupiter,_Pluton).wav'
-rw-r--r-- 1 george george 34762604 Dec 24 21:34 '04.Orphée_aux_enfers__Acte_deuxième._Air_en_prose_de_Pluton_Comme_il_me_regarde!_(Pluton).wav'
-rw-r--r-- 1 george george  3838508 Dec 24 21:34  03.Orphée_aux_enfers__Acte_deuxième._Entrée_de_Pluton.wav
-rw-r--r-- 1 george george  5840060 Dec 24 21:34 '02.Orphée_aux_enfers__Acte_deuxième._Dialogue_Salut_au_puissant_maître_des_dieux..._(Mercure,_Jupiter,_Junon).wav'
-rw-r--r-- 1 george george 24390284 Dec 24 21:34 '01.Orphée_aux_enfers__Acte_deuxième._Rondo_saltarelle_de_Mercure_Eh_hop!_Eh_hop!_(Mercure,_Junon,_Jupiter).wav'
```
In this case, 
- files named `file-001.wav` are located in the current folder,
- files named `01.Orphée_aux_enfers.wav` are located in the parent folder (`..`).  
We would generate two file listings, containing only file sizes, for the two folders we'd like to compare.  
```
ls -lt *.wav | grep -P \^\.\*file\.\*\$ | cut -d" " -f5|sed 's/^ *//; s/ *$//; /^$/d; /^\s*$/d' > diff-file.txt
ls -lt *.wav .. | grep -P \^\.\*Orp\.\*\$ | cut -d" " -f5|sed 's/^ *//; s/ *$//; /^$/d; /^\s*$/d' > diff-Orp.txt
```

To see one of the generated file listings, run the below:  
```
[george@archie test]$ cat diff-file.txt 
30336140
29823404
12971324
54625244
31293404
```
Then, let's compare the generated file listings.
```
diff -s diff-file.txt diff-Orp.txt
```

As an example, the two file listings are identical:  
```
[george@archie test]$ diff -s diff-file.txt diff-Orp.txt 
Files diff-file.txt and diff-Orp.txt are identical
```
