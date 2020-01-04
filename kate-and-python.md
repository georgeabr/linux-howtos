
Run this, if issues:  
```
pip install -U setuptools
```

Uninstall `pyls` and `python-language-server` and install only `python-language-server`.  
```
sudo pip3 uninstall pyls python-language-server
sudo pip3 install python-language-server
```

Add the line below to the `build` target in `Kate`:  
```
konsole -e sh -c 'python3 "%f";printf "\n"; read -n 1 -s -r -p "Press any key to continue" '
```
