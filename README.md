# utl-group-by-a-sequence-of-seven-morning-glucose-levels-max-min-median
Group by a sequence of seven morning glucose levels max min median

    Group by a sequence of seven morning glucose levels max min median                                                              
                                                                                                                                    
    DOSUBL makes you think differently about SAS processing?                                                                        
                                                                                                                                    
    This is an interesting problem because it cannot be EASILY solved using                                                         
                                                                                                                                    
      1. Proc SQL (have solution below)                                                                                             
      2. Merge or mutiple sets                                                                                                      
      3. Format                                                                                                                     
      4. DOSUBL (have solution below)                                                                                               
                                                                                                                                    
      It is a shame that _n_ is available in a where clause.                                                                        
                                                                                                                                    
    Three Solutions                                                                                                                 
                                                                                                                                    
         1. View then summary                                                                                                       
         2. SQL (comples - uses montotonic)                                                                                         
         3. DOSUBL ( hokey but demonstates the flexibility od DOSUBL                                                                
             a. sasfile is used to minimize overhead (need smart compiler)                                                          
             b. uses open=defer for fast proc append?                                                                               
             c. dosubl proc summary in same address space                                                                           
             d. passing datasteps to and from dosubl                                                                                
                                                                                                                                    
    github                                                                                                                          
    https://tinyurl.com/y56fydzv                                                                                                    
    https://github.com/rogerjdeangelis/utl-group-by-a-sequence-of-seven-morning-glucose-levels-max-min-median                       
                                                                                                                                    
    *_                   _                                                                                                          
    (_)_ __  _ __  _   _| |_                                                                                                        
    | | '_ \| '_ \| | | | __|                                                                                                       
    | | | | | |_) | |_| | |_                                                                                                        
    |_|_| |_| .__/ \__,_|\__|                                                                                                       
            |_|                                                                                                                     
    ;                                                                                                                               
    data have;                                                                                                                      
     call streaminit(1234);                                                                                                         
      do rec=1 to 3;                                                                                                                
        do day= 1 to 7;                                                                                                             
          glucose = round(abs(50*rand('normal')) + 100);                                                                            
          output;                                                                                                                   
        end;                                                                                                                        
      end;                                                                                                                          
      drop rec;                                                                                                                     
    run;quit;                                                                                                                       
                                                                                                                                    
    Up to 40 obs WORK.HAVE total obs=21                                                                                             
                                                                                                                                    
                           | Rules                                                                                                  
    Obs    DAY    GLUCOSE  |               SORTED                                                                                   
                           |                                                                                                        
      1     1       143    |                128                                                                                     
      2     2       141    | MIN    = 128   141                                                                                     
      3     3       152    | MAX    = 187   143                                                                                     
      4     4       153    | MEDIAN = 152   152 Median                                                                              
      5     5       187    |                153                                                                                     
      6     6       128    |                187                                                                                     
      7     7       173    |                173                                                                                     
                                                                                                                                    
      8     1       113                                                                                                             
      9     2       133                                                                                                             
     10     3       121                                                                                                             
     11     4       103                                                                                                             
     12     5       191                                                                                                             
     13     6       104                                                                                                             
     14     7       123                                                                                                             
                                                                                                                                    
     15     1       130                                                                                                             
     16     2       115                                                                                                             
     17     3       116                                                                                                             
     18     4       130                                                                                                             
     19     5       147                                                                                                             
     20     6       128                                                                                                             
     21     7       148                                                                                                             
                                                                                                                                    
    *            _               _                                                                                                  
      ___  _   _| |_ _ __  _   _| |_                                                                                                
     / _ \| | | | __| '_ \| | | | __|                                                                                               
    | (_) | |_| | |_| |_) | |_| | |_                                                                                                
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                               
                    |_|                                                                                                             
    ;                                                                                                                               
                                                                                                                                    
    Up to 40 obs WORK.WANT total obs=3                                                                                              
                                                                                                                                    
                  GLUCOSE_    GLUCOSE_    GLUCOSE_                                                                                  
    Obs    GRP       MAX         MIN       MEDIAN                                                                                   
                                                                                                                                    
     1      1        187         128         152                                                                                    
     2      2        191         103         121                                                                                    
     3      3        148         115         130                                                                                    
                                                                                                                                    
                                                                                                                                    
    *_                                                                                                                              
    / |   __   ___   _  ___   _ __ ___   ___  __ _ _ __  ___                                                                        
    | |   \ \ / / | | |/ _ \ | '_ ` _ \ / _ \/ _` | '_ \/ __|                                                                       
    | |_   \ V /| |_| |  __/ | | | | | |  __/ (_| | | | \__ \                                                                       
    |_(_)   \_/  \__,_|\___| |_| |_| |_|\___|\__,_|_| |_|___/                                                                       
                                                                                                                                    
    ;                                                                                                                               
                                                                                                                                    
    data havVue / view=havVue;                                                                                                      
      retain grp 0;                                                                                                                 
      set have;                                                                                                                     
      if day=1 then grp+1;                                                                                                          
    run;quit;                                                                                                                       
                                                                                                                                    
    proc summary data=havVue nway;                                                                                                  
    by grp;                                                                                                                         
    var glucose;                                                                                                                    
    output out=want(drop=_:) max= min= median= /autoname;                                                                           
    run;quit;                                                                                                                       
                                                                                                                                    
    *____                 _                                                                                                         
    |___ \      ___  __ _| |                                                                                                        
      __) |    / __|/ _` | |                                                                                                        
     / __/ _   \__ \ (_| | |                                                                                                        
    |_____(_)  |___/\__, |_|                                                                                                        
                       |_|                                                                                                          
    ;                                                                                                                               
                                                                                                                                    
    proc sql;                                                                                                                       
      create                                                                                                                        
         table want as                                                                                                              
      select                                                                                                                        
         ((seq - mod(seq-1,7)) -1)/7 +1 as grp                                                                                      
        ,mean(glucose)     as  mean_glucose                                                                                         
        ,max(glucose)      as  max_glucose                                                                                          
        ,min(glucose)      as  min_glucose                                                                                          
        ,median(glucose)   as  median_glucose                                                                                       
                                                                                                                                    
      from                                                                                                                          
      (                                                                                                                             
      select                                                                                                                        
         monotonic() as seq                                                                                                         
        ,glucose                                                                                                                    
      from                                                                                                                          
         have                                                                                                                       
      )                                                                                                                             
      group                                                                                                                         
         by seq - mod(seq-1,7)                                                                                                      
    ;quit;                                                                                                                          
                                                                                                                                    
    *_____        _                 _     _                                                                                         
    |___ /     __| | ___  ___ _   _| |__ | |                                                                                        
      |_ \    / _` |/ _ \/ __| | | | '_ \| |                                                                                        
     ___) |  | (_| | (_) \__ \ |_| | |_) | |                                                                                        
    |____(_)  \__,_|\___/|___/\__,_|_.__/|_|                                                                                        
                                                                                                                                    
    ;                                                                                                                               
                                                                                                                                    
    proc datasets lib=work;                                                                                                         
      delete want:;                                                                                                                 
    run;quit;                                                                                                                       
                                                                                                                                    
    data have;                                                                                                                      
     call streaminit(1234);                                                                                                         
     retain glucose_min glucose_max glucose_mean 0;                                                                                 
      do rec=1 to 3;                                                                                                                
        do day= 1 to 7;                                                                                                             
          glucose = round(abs(50*rand('normal')) + 100);                                                                            
          output;                                                                                                                   
        end;                                                                                                                        
      end;                                                                                                                          
      drop rec;                                                                                                                     
    run;quit;                                                                                                                       
                                                                                                                                    
    sasfile have load;                                                                                                              
    data want (where=(grp ne .));                                                                                                   
                                                                                                                                    
        retain grp;                                                                                                                 
                                                                                                                                    
        set have(in=a where=(day=1)) want1  want2  want3 open=defer ;                                                               
                                                                                                                                    
        if  not a then grp+1;                                                                                                       
                                                                                                                                    
        call symputx("grp",_n_);                                                                                                    
        call symputx("obs",(_n_* 7));                                                                                               
        call symputx("firstobs",((_n_-1) * 7 + 1));                                                                                 
                                                                                                                                    
        if (_n_* 7) < 28 then do;                                                                                                   
                                                                                                                                    
           rc=dosubl('                                                                                                              
              %put &=obs;                                                                                                           
              proc summary data=have(firstobs=&firstobs obs=&obs) nway;                                                             
              var glucose;                                                                                                          
              output out=want&grp (drop=_:) max= min= median= mean= /autoname;                                                      
              run;quit;                                                                                                             
           ');                                                                                                                      
                                                                                                                                    
        end;                                                                                                                        
                                                                                                                                    
    keep grp glucose_min glucose_max glucose_mean;                                                                                  
                                                                                                                                    
    run;quit;                                                                                                                       
                                                                                                                                    
    sasfile have close;                                                                                                             
