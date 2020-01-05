1. Enable `LSP Client` plugin in `Kate`  

2. Download this file and add it to the `LSP client` config in `Kate`  
https://invent.kde.org/kde/kate/blob/master/addons/lspclient/settings.json

3. Install `LSP` for python3, with `sudo` maybe. See this - https://github.com/palantir/python-language-server:
```
pip3 install python-language-server
```   
* Run this, if issues:  
```
pip install -U setuptools
```

* Uninstall `pyls` and `python-language-server` and install only `python-language-server`.  
```
sudo pip3 uninstall pyls python-language-server
sudo pip3 install python-language-server
```

4. Enable `Build plugin` in `Kate`.  
Add the line below to the `build` target in `Kate`:  
```
konsole -e sh -c 'python3 "%f";printf "\n"; read -n 1 -s -r -p "Press any key to continue" '
```

Use `Projects` in `Kate` for your files.  
They will be managed as a project by `Kate` (all files present in `Projects` tab).  
Create the file `.kateproject` (exactly this name) in a folder where you have `.py` files.
Paste the contents below in this file. Escape characters in the `build` command `\` -> `\\`, `"` -> `\"`.   
More about escape characters in JSON - https://www.freeformatter.com/json-escape.html  

```
{
"name": "Python lessons",
"files": 
	[ 
		{ 
			"directory": ".",
			"filters": ["*.py"], 
			"recursive": 1 
		}	 
	],
"build": 
	{
 		"build": "konsole -e sh -c 'python3 \"%f\";printf \"\\n\"; read -n 1 -s -r -p \"Press any key TO continue\" '"
  	}

}
```
