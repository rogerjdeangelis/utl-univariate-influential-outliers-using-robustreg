# utl-univariate-influential-outliers-using-robustreg
Univariate influential outliers using robustreg.

    Univariate influential outliers using robustreg                                                                              
                                                                                                                                 
    see github                                                                                                                   
    https://tinyurl.com/ycvca2pv                                                                                                 
    https://github.com/rogerjdeangelis/utl-univariate-influential-outliers-using-robustreg                                       
                                                                                                                                 
    Of course there is always Tukey's box plots.                                                                                 
    Also Grub's test abd R Outlier packages. Will post both to my github                                                         
                                                                                                                                 
    SAS Forum                                                                                                                    
    https://tinyurl.com/ya2zkzwn                                                                                                 
    https://communities.sas.com/t5/New-SAS-User/Influential-and-Outliers/m-p/502868                                              
                                                                                                                                 
    Macros                                                                                                                       
    https://tinyurl.com/y9nfugth                                                                                                 
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories                                   
                                                                                                                                 
                                                                                                                                 
    INPUT                                                                                                                        
    =====                                                                                                                        
                                                                                                                                 
     WORK.HART total obs=5,209                                                                                                   
                                                                                                                                 
        HEIGHT     WEIGHT                                                                                                        
                                                                                                                                 
       62.5000    140.000                                                                                                        
       59.7500    194.000                                                                                                        
        2.4381      0.895   ** outlier?                                                                                          
       65.7500    158.000                                                                                                        
       66.0000    156.000                                                                                                        
       61.7500    131.000                                                                                                        
       64.7500    136.000                                                                                                        
       65.5000    130.000                                                                                                        
       71.0000    194.000                                                                                                        
       62.5000    129.000                                                                                                        
       66.2500    179.000                                                                                                        
                                                                                                                                 
                                                                                                                                 
    EXAMPLE OUTPUT (only printed output)                                                                                         
    ------------------------------------                                                                                         
                                                                                                                                 
    Does all variables in dataset                                                                                                
                                                                                                                                 
     Top 30 Variable=HEIGHT Cutoff=2.93 Expected Outliers=18;                                                                    
                                                                                                                                 
          Obs       OBS    RRESIDUAL         OUTLIER                                                                             
                                                                                                                                 
            1         3    -16.9463         1.000000                                                                             
            2      2247     -3.5892         1.000000                                                                             
            3      3880      3.2170         1.000000                                                                             
            4      3353      3.0809         1.000000                                                                             
            5      1891      3.0809         1.000000                                                                             
            6      3506     -2.9767         1.000000                                                                             
            7       678      2.9447         1.000000                                                                             
            8       418      2.9447         1.000000                                                                             
           ...                                                                                                                   
                                                                                                                                 
    Top 30 Variable=WEIGHT Cutoff=2.93 Expected Outliers=18;                                                                     
                                                                                                                                 
         Obs       OBS    RRESIDUAL         OUTLIER                                                                              
                                                                                                                                 
           1         3     -5.2265         1.000000                                                                              
           2      4700      5.2126         1.000000                                                                              
           3      3357      5.2126         1.000000                                                                              
           4      3249      4.9683         1.000000                                                                              
           5      4854      4.5495         1.000000                                                                              
           6      2117      4.3750         1.000000                                                                              
           7      5129      4.3401         1.000000                                                                              
          ...                                                                                                                    
                                                                                                                                 
                                                                                                                                 
    PROCESS                                                                                                                      
    =======                                                                                                                      
                                                                                                                                 
       %utl_regOutLyr(data=work.hart);                                                                                           
                                                                                                                                 
                                                                                                                                 
    OUTPUT                                                                                                                       
    ======                                                                                                                       
                                                                                                                                 
       see above                                                                                                                 
                                                                                                                                 
    *                _               _       _                                                                                   
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _                                                                            
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |                                                                           
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |                                                                           
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|                                                                           
                                                                                                                                 
    ;                                                                                                                            
                                                                                                                                 
    * only needed for development;                                                                                               
    %symdel vars cutoff expout data/nowarn;                                                                                      
                                                                                                                                 
    data hart;                                                                                                                   
     set sashelp.heart(keep=height weight);                                                                                      
     if _n_=3 then do;                                                                                                           
           height=10*uniform(1234);                                                                                              
           weight=10*uniform(1234);                                                                                              
     end;                                                                                                                        
    run;quit;                                                                                                                    
                                                                                                                                 
    *                                                                                                                            
     _ __ ___   __ _  ___ _ __ ___                                                                                               
    | '_ ` _ \ / _` |/ __| '__/ _ \                                                                                              
    | | | | | | (_| | (__| | | (_) |                                                                                             
    |_| |_| |_|\__,_|\___|_|  \___/                                                                                              
                                                                                                                                 
    ;                                                                                                                            
                                                                                                                                 
    %macro utl_regOutLyr(data=hart,out=want);                                                                                    
                                                                                                                                 
    /* %let data=hart; %let vars=height; */                                                                                      
                                                                                                                                 
    proc sql noprint; select count(*) into :nobs from &data;quit;                                                                
                                                                                                                                 
    data _null_;                                                                                                                 
                                                                                                                                 
     length dsn $32;                                                                                                             
                                                                                                                                 
     do vars=%utl_varlist(&data,keep=_numeric_,qstyle=DOUBLE,od=%str(,));                                                        
                                                                                                                                 
        call symputx('vars',vars);                                                                                               
                                                                                                                                 
        rc=dosubl('                                                                                                              
           data _null_;                                                                                                          
             * (e^x)^2) -> sqrt(log(x));                                                                                         
             cutoff=sqrt(log(&nobs));                                                                                            
             call symputx("cutoff",put(cutoff,5.2));                                                                             
             expected_outliers=2 * &nobs * (1-cdf("normal",cutoff));                                                             
             call symputx("expout",round(expected_outliers));                                                                    
           run;quit;                                                                                                             
                                                                                                                                 
           ods exclude all;                                                                                                      
           ods output diagnostics=__vvdag;                                                                                       
           proc robustreg data=&data method=MM;                                                                                  
           model &vars = /diagnostics  cutoff=&cutoff /* stadardized sigma &cutoff * sigma */;                                   
           run;                                                                                                                  
           ods select all;                                                                                                       
           ods listing;                                                                                                          
                                                                                                                                 
           proc sql;                                                                                                             
              create                                                                                                             
                 view __vvdagtbl as                                                                                              
              select                                                                                                             
                 *                                                                                                               
              from                                                                                                               
                 __vvdag                                                                                                         
              order                                                                                                              
                 by abs(rresidual) descending                                                                                    
           ;quit;                                                                                                                
                                                                                                                                 
           proc sql noprint; select count(*) into :regobs trimmed from __vvdag;quit;                                             
                                                                                                                                 
           %sysfunc(ifc(&regobs ne 0                                                                                             
                ,%str(proc print data=__vvdagtbl(obs=30);title "Top 30 Variable=&vars Cutoff=&cutoff Expected Outliers=&expout;")
                ,%str()));                                                                                                       
                                                                                                                                 
           run;quit;                                                                                                             
        ');                                                                                                                      
                                                                                                                                 
     end;                                                                                                                        
     stop;                                                                                                                       
    run;quit;                                                                                                                    
                                                                                                                                 
    proc datasets lib=work;                                                                                                      
     delete __vvdag;                                                                                                             
    run;quit;                                                                                                                    
                                                                                                                                 
    %mend utl_regOutLyr;                                                                                                         
                                                                                                                                 
                                                                                                                                 
