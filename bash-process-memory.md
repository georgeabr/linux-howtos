Run the below to show memory usage in megabytes for top 15 processes:
```
ps -axco size,pid,user,command | awk '{ hr=$1/1024 ; printf("%13.0f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' | sort -nr | head -n 15
```

Output will be:  
```
          734 Mb firefox-bin 
          558 Mb Web Content 
          488 Mb Web Content 
          460 Mb Web Content 
          423 Mb Web Content 
          372 Mb Web Content 
          368 Mb WebExtensions 
          360 Mb Web Content 
          343 Mb Web Content 
          225 Mb Web Content 
          183 Mb Xwayland 
          101 Mb sddm-greeter 
           96 Mb kitty 
           90 Mb kitty 
           87 Mb kitty 
```
