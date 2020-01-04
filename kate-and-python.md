1. Enable `LSP Client` plugin in `Kate`  

2. Download this file and add it to the `LSP client` config in `Kate`  
https://invent.kde.org/kde/kate/blob/master/addons/lspclient/settings.json

3. Install `LSP` for python3, with `sudo` maybe. See this - https://github.com/palantir/python-language-server:  
```
pip3 install python-language-server
```

    3.1 Run this, if issues:  
```
pip install -U setuptools
```

    3.2 Uninstall `pyls` and `python-language-server` and install only `python-language-server`.  
```
sudo pip3 uninstall pyls python-language-server
sudo pip3 install python-language-server
```

4. Enable `Build plugin` in `Kate`.  
Add the line below to the `build` target in `Kate`:  
```
konsole -e sh -c 'python3 "%f";printf "\n"; read -n 1 -s -r -p "Press any key to continue" '
```
