Welcome to the akw-sed-grep wiki!

# sed, awk and grep

In bioinformatics, we regulary work with large files that is often too big to manually manipulate or with many small files that is to tedious to do the same repetative task for each individual file. Luckily there are many Unix tools available for searching and scuplting text based a pattern (regular expression). Here I will show you three tools: sed, awk and grep, which are often used for text searching and file manipulation. All of these tools can either be run on directly on files or as part of a pipeline.

In the following examples, we will use the isoforms.fpkm_tracking file from cuffdiff48_3

## sed

sed (stream editor) is a tool that parses and transforms text. sed is often used to replace or remove patterns from a text file.
Lets say you want to remove the trailing ".v2.0" at the end of the isoform list, the command:

    sed 's/.v2.0//g' isoforms.fpkm_tracking

will remove the specified pattern from the file

    Eucgr.A00001.1   -	-	Eucgr.A00001	Eucgr.A00001	-	Chr01:2787013-2792340	2184	-	33.1904	23.060343.3017 OK 31.9871 22.1794 41.7748 OK
    Eucgr.A00003.1	 -	-	Eucgr.A00003	Eucgr.A00003	-	Chr01:2759420-2762078	1314	-	23.199614.804331.5934 OK 23.9346 15.39	  32.4719 OK

You can also replace the "Eucgr" with the shortened species name by running:

    sed 's/Eucgr/E.grandis/g' isoforms.fpkm_tracking

    E.grandis.A00001.1.v2.0	     -	-	E.grandis.A00001.v2.0	E.grandis.A00001	-	Chr01:2787013-2792340	2184 -33.1904 23.0603 43.3017 OK 31.9871 22.1794 41.7748 OK
    E.grandis.A00003.1.v2.0	     -	-	E.grandis.A00003.v2.0	E.grandis.A00003	-	Chr01:2759420-2762078	1314 -23.1996 14.8043 31.5934 OK 23.9346 15.39	 32.4719 OK


## AWK

AWK is a data-driven scripting language which consists of a set of actions to be performed against textual data. It is commonly used for text processing and data extraction.
AWK is very useful for processing TSV files like the isoforms.fpkm_tracking file. Lets say we want all isoforms with detectable expression (i.e. FPKM values over 1) in the control, you can use AWK to print all lines where the FPKM values of the control are over 1.

    awk '$10 > 1 {print}' isoforms.fpkm_tracking

This command would show all isoforms with a control FPKM higher than 1

    Eucgr.A00001.1.v2.0	-   -	     Eucgr.A00001.v2.0	 Eucgr.A00001	-	Chr01:2787013-2792340	2184	-	33.1904 23.0603 43.3017 OK 31.9871 22.1794 41.7748 OK
    Eucgr.A00003.1.v2.0	-   -	     Eucgr.A00003.v2.0	 Eucgr.A00003	-	Chr01:2759420-2762078	1314	-	23.199614.8043	31.5934	OK 23.9346 15.39   32.4719 OK
    Eucgr.A00010.2.v2.0	-   -	     Eucgr.A00010.v2.0	 Eucgr.A00010	-	Chr01:2711619-2714292	800	-	14.19777.91512	20.4862	OK 14.6416 8.08387 21.1373 OK
    Eucgr.A00012.1.v2.0	-   -	     Eucgr.A00012.v2.0	 Eucgr.A00012	-	Chr01:2682570-2688071	1482	-	8.647195.09901	12.1989	OK 9.08831 5.44394 12.724  OK
    Eucgr.A00014.1.v2.0	-   -	     Eucgr.A00014.v2.0	 Eucgr.A00014	-	Chr01:2665012-2666165	671	-	2.469570.892026	4.01412	   OK	   3.93274 1.5929 6.24418 OK
    Eucgr.A00015.1.v2.0	-   -	     Eucgr.A00015.v2.0	 Eucgr.A00015	-	Chr01:2658246-2661013	2767	-	2.940631.69963		4.17183	   OK	   3.36946 1.99321	  4.74353 OK
    Eucgr.A00016.1.v2.0	-   -	     Eucgr.A00016.v2.0	 Eucgr.A00016	-	Chr01:2653888-2657909	977	-	15.77419.14585		22.3176	   OK	   16.7291 9.71473	  23.5867 OK

You can specify multiple criteria i.e. to print all lines where the control FPKM is over 1 and the h48pi FPKM is less than 1

    awk '$10 > 1 && $14 < 1 {print}' isoforms.fpkm_tracking

    Eucgr.A00032.1.v2.0 -	- Eucgr.A00032.v2.0	Eucgr.A00032	-	Chr01:2526775-2529499	931	-	1.190070.4133 1.92873 OK 0.759336 0.275533 1.28582 OK
    Eucgr.A00037.2.v2.0 -	- Eucgr.A00037.v2.0	Eucgr.A00037	-	Chr01:2452862-2458039	1986	-	1.848060      4.51199 OK 0.261458 0	    1.51755 OK
    Eucgr.A00080.2.v2.0 -	- Eucgr.A00080.v2.0	Eucgr.A00080	-	Chr01:1663976-1666451	986	-	1.251020      2.83724 OK 0.902237 0	    2.46884 OK
    Eucgr.A00130.1.v2.0 -	- Eucgr.A00130.v2.0	Eucgr.A00130	-	Chr01:7324618-7325642	864	-	1.4385	0.544316       2.32571	   OK	    0.909795 0.2969 1.4845 OK
    Eucgr.A00136.1.v2.0 -	- Eucgr.A00136.v2.0	Eucgr.A00136	-	Chr01:7379025-7380244	732	-	1.066570.350439       1.8106	   OK	    0.457153 0.0584065	   0.817691 OK
    Eucgr.A00137.1.v2.0 -	- Eucgr.A00137.v2.0	Eucgr.A00137	-	Chr01:7384507-7385608	598	-	1.087520.285977       1.85885	   OK	    0.887047 0.142988	   1.57287  OK
    Eucgr.A00288.1.v2.0 -	- Eucgr.A00288.v2.0	Eucgr.A00288	-	Chr01:2083536-2087086	2461	-	1.495670       3.22909	   OK	    0.380098 0		   1.13084  OK
    Eucgr.A00439.1.v2.0 -	- Eucgr.A00439.v2.0	Eucgr.A00439	-	Chr01:9473893-9474930	835	-	1.091510.358413       1.79206	   OK	    0.857074 0.307211	   1.43365  OK
    Eucgr.A00497.1.v2.0 -	- Eucgr.A00497.v2.0	Eucgr.A00497	-	Chr06:29469558-29474818	1263	-	1.016990       3.36374	   OK	    0.00313389		   0	    0.0771566 OK
    Eucgr.A00507.3.v2.0 -	- Eucgr.A00507.v2.0	Eucgr.A00507	-	Chr01:17654257-17659142	1158	-	1.431560       3.07586	   OK	    0.912944		   0	    2.30126   OK

And if you only want the isoform ids and their chromosome location of these isoforms:

    awk '$10 > 1 && $14 < 1 {print $1 "\t" $7}' isoforms.fpkm_tracking

    Eucgr.A00032.1.v2.0 Chr01:2526775-2529499
    Eucgr.A00037.2.v2.0 Chr01:2452862-2458039
    Eucgr.A00080.2.v2.0 Chr01:1663976-1666451
    Eucgr.A00130.1.v2.0 Chr01:7324618-7325642
    Eucgr.A00136.1.v2.0 Chr01:7379025-7380244
    Eucgr.A00137.1.v2.0 Chr01:7384507-7385608
    Eucgr.A00288.1.v2.0 Chr01:2083536-2087086
    Eucgr.A00439.1.v2.0 Chr01:9473893-9474930
    Eucgr.A00497.1.v2.0 Chr06:29469558-29474818
    Eucgr.A00507.3.v2.0 Chr01:17654257-17659142

## grep

The grep (globally search a regular expression and print) tool is used to search plain text files for lines that match a regualr expression.
The simplest way people use grep is to search the content of their files for lines containing a word/pattern of interest. for example if you want to find all genes originating from a specific chromosome, you can search for lines containing the chromosome number.

Runnung the following command:

    grep Chr07 isoforms.fpkm_tracking

will give us a list of all isoforms on chromosome 7:

    Eucgr.A00117.1.v2.0	   -	    -	Eucgr.A00117.v2.0	Eucgr.A00117	-	Chr07:10035635-10038882	2430	- 39.3017 27.4819 51.0755 OK 38.3999 26.8485 49.9143 OK
    Eucgr.A00118.1.v2.0	   -	    -	Eucgr.A00118.v2.0	Eucgr.A00118	-	Chr07:10041492-10043976	844	- 0.0626629	  0	  0.253279   OK	     0.023171 0 0.151968 OK
    Eucgr.A00119.1.v2.0	   -	    -	Eucgr.A00119.v2.0	Eucgr.A00119	-	Chr07:10053636-10057389	1289	- 00	  0	  OK	     0.0382241	      0	0.16584	 OK
    Eucgr.B00907.1.v2.0	   -	    -	Eucgr.B00907.v2.0	Eucgr.B00907	-	Chr07:49816698-49817292	594	- 00	  0	  OK	     0		      0	0	 OK
    Eucgr.B00910.1.v2.0	   -	    -	Eucgr.B00910.v2.0	Eucgr.B00910	-	Chr07:49749793-49751227	561	- 2.9473  1.06693 4.8012     OK		      2.78082	 0.990724 4.49636 OK
    Eucgr.B00911.1.v2.0	   -	    -	Eucgr.B00911.v2.0	Eucgr.B00911	-	Chr07:49739383-49740183	800	- 00	  0	  OK	     0		      0		 0	  OK
    Eucgr.B00912.1.v2.0	   -	    -	Eucgr.B00912.v2.0	Eucgr.B00912	-	Chr07:49681799-49682336	537	- 0.146405	  0	  0.398078   OK		      0.230958	 0	  0.398078 OK
    Eucgr.B00913.1.v2.0	   -	    -	Eucgr.B00913.v2.0	Eucgr.B00913	-	Chr07:49609430-49612375	1107	- 0.19824	  0.0386211	     0.386211	      OK	 0.170817 0	   0.308969 OK
    Eucgr.B00914.1.v2.0	   -	    -	Eucgr.B00914.v2.0	Eucgr.B00914	-	Chr07:49588743-49592115	1284	- 00	  0		     OK		      0		 0	  0	   OK
    Eucgr.G00002.1.v2.0	   -	    -	Eucgr.G00002.v2.0	Eucgr.G00002	-	Chr07:55775-56414	639	- 00	  0		     OK		      0		 0	  0	   OK

Pattern searching in grep is case sensitive and in certain cases, you might want to ignore the case of the word. Using the -i flag will ignore case and print all lines containing that word regarldess of case. Running

    grep -i eucgr.a00117 isoforms.fpkm_tracking

will yield

    Eucgr.A00117.1.v2.0	-	-	Eucgr.A00117.v2.0	Eucgr.A00117	-	Chr07:10035635-10038882	2430	- 39.3017 27.4819 51.0755 OK 38.3999 26.8485 49.9143 OK
