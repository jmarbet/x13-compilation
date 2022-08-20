# Compiling X-13ARIMA-SEATS from Source for macOS Monterey

This guide shows how to compile X-13ARIMA-SEATS on macOS Monterey and is essentially an updated version of the excellent guide by Christoph Sax ([see here](https://github.com/christophsax/seasonal/wiki/Compiling-X-13ARIMA-SEATS-from-Source-for-OS-X)).

1. Install the latest version of gfortran (12.1) from [here](http://coudert.name/software.html)

2. Download the source code package `x13as_asciisrc-v1-1-b59.tar.gz` from the [Census website](https://www.census.gov/data/software/x13as.X-13ARIMA-SEATS.html). You can find the file under `Download -> Linux/Unix Version Archives` ([Direct Link](https://www2.census.gov/software/x-13arima-seats/x13as/unix-linux/program-archives/x13as_asciisrc-v1-1-b59.tar.gz))

3. Double-click to unpack the file. You should get a directory named `x13as_asciisrc-v1-1-b59`

4. Go to the `x13as_asciisrc-v1-1-b59` directory, open `makefile.gf` in a text editor and remove the -static option on line 293, which otherwise causes an error. Change the line

   ```
      $(LINKER) -static -o $@ $(OBJS) $(LDMAP) $(LIBS) $(LDFLAGS)
   ```

   to 

   ```
      $(LINKER) -o $@ $(OBJS) $(LDMAP) $(LIBS) $(LDFLAGS)
   ```

5. If you downloaded an older version, you might also need to change the following lines

   ```
   FC        = /usr/bin/g77
   LINKER    = /usr/bin/g77
   ```

   to

   ```
   FC        = gfortran
   LINKER    = gfortran
   ```

6. Save the file as `makefile.gf`

7. Open `adpdrg.f` in the text editor and change line 409 as follows

   ```
        &               'after end of LS sequence (LSS)',T)
   ```

   to

   ```
        &               'after end of LS sequence (LSS)')
   ```

   otherwise you get an error during the compilation.

   Note: This might not be necessary if you are compiling a newer or an older version than `Version 1.1, Build 59`. If you are unsure about whether you need to make the change, try to continue with the guide. If you get an error message in step 10 regarding line 409 in `adpdrg.f`, you will need to make this change to be able to successfully compile it.

8. Open the terminal and install the Xcode command line tools using the following command

   ```
   xcode-select --install
   ```

   Confirm that you want to install the command line tools by clicking on `Install` in the popup window.

9. In the terminal, go to the downloaded directory

   ```
   cd ~/Downloads/x13as_asciisrc-v1-1-b59
   ```

10. Compile the code using

 	```
	make -f makefile.gf
 	```

11. You can test your compilation in the terminal using

    ```
    ./x13as_ascii
    ```
	
    If everything went fine, you should get the following error that proves that the binary file is working
   
	```
 	X-13ARIMA-SEATS Seasonal Adjustment Program
 	Version Number 1.1 Build 59
 	Execution began  Jul 14, 2022  19.38.34 
	
 	ERROR: Must specify either an input specification file name
	        (-i infile or infile) or an input metafile name (-m metafile).
	
	        See Section 2 of the X-13ARIMA-SEATS Reference Manual for more
	        information on how to run X-13ARIMA-SEATS.
	```

12. You can move the newly created `x13as_ascii` binary file to anywhere you want. To use the binary in Python, you would need to write (in Python)

    ```
    import statsmodels.api as sm
    res = sm.tsa.x13_arima_analysis(s, x12path = '~/Downloads/x13as_asciisrc-v1-1-b59/')
    ```
	
    where you would also need to define the series `s` on which you would like to do the seasonal adjustment.

    If you want to install it in your path, you can enter the following command in the terminal

	```
    cp ~/Downloads/x13as_asciisrc-v1-1-b59/x13as_ascii /usr/local/bin/x13as_ascii
   	```
	
    Then you don't need to supply the argument `x12path` to the function, e.g.
	
    ```
    res = sm.tsa.x13_arima_analysis(s)
    ```

    would suffice.