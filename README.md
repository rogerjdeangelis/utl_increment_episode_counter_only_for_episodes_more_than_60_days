# utl_increment_episode_counter_only_for_episodes_more_than_60_days
Increment_episode_counter_only_for_episodes_more_than_60_days.
    Increment_episode_counter_only_for_episodes_more_than_60_days;

      WPS and SAS produces exactly the same result

    github
    https://goo.gl/BZcjfj
    https://github.com/rogerjdeangelis/utl_increment_episode_counter_only_for_episodes_more_than_60_days

    see
    https://goo.gl/Cqyv6e
    https://communities.sas.com/t5/Base-SAS-Programming/Counting-observations-within-a-by-group-based-on-specific/m-p/435304

    Astounding
    https://communities.sas.com/t5/user/viewprofilepage/user-id/4954


    INPUT
    =====

    ALGORITHM (line counts episodes > 60 days)

         retain baseline_date .
         if first.id set line=1 and baseline_date=date_start;
          do not increment line counter unless date_start - baseline_date > 60
          at which time line=line+1 and set a new baseline_date as date_start

          Key is only change baseline date  when date_start - baseline_date > 60
          not on all occurances of date - lage date >60


     WORK.HAVE total obs=14

              START_      DATE_   | RULE
      ID      DATEC       START   | LINE
                                  |
      1     2010-07-02    18445   |
      1     2011-08-17    18856   |
      1     2011-09-09    18879   |
      1     2011-11-14    18945   |
                                  |      START BASE   DIF  LINE
      2     2007-03-17    17242   |  1   first.id line=1    1
      2     2007-06-29    17346   |  2   17346-17242  104   2  >60 increment and set new baseline 17346
      2     2007-07-17    17364   |  2   17364-17346  28    2  do not increment. Note baseline is not updated
      2     2007-08-14    17392   |  2   17392-17346  46    2  do not increment. Note baseline is not updated
      2     2007-09-04    17413   |  3   17413-17346  67    3  >60 increment and set new baseline 17413
      2     2008-02-18    17580   |  4   17580-17413  167   4  >60 increment and set new baseline 17580
      2     2008-03-21    17612   |  4   17612-17580  32    4  do not increment. Note baseline is not updated
      2     2008-04-15    17637   |  4   17637-17580  57    4  do not increment. Note baseline is not updated
      2     2008-04-29    17651   |  5   17651-17580  71    5  >60 increment and set new baseline 17651
      2     2008-05-15    17667   |  5   17667-17651  16    5  do not increment. Note baseline is not updated



    PROCESS (all the code)
    ======================

      data want;
        retain id;
        set have;
         by id;
         if first.id then do;
            line=1;
            baseline_date = date_start;
         end;
         else if date_start - baseline_date > 60 then do;
            line + 1;
            baseline_date = date_start;
         end;
         retain baseline_date;
         drop baseline_date;
      run;quit;


    OUTPUT
    ======

    WORK.WANT total obs=14

             START_      DATE_
     ID      DATEC       START    LINE

      1    2010-07-02    18445      1
      1    2011-08-17    18856      2
      1    2011-09-09    18879      2
      1    2011-11-14    18945      3
      2    2007-03-17    17242      1
      2    2007-06-29    17346      2
      2    2007-07-17    17364      2
      2    2007-08-14    17392      2
      2    2007-09-04    17413      3
      2    2008-02-18    17580      4
      2    2008-03-21    17612      4
      2    2008-04-15    17637      4
      2    2008-04-29    17651      5
      2    2008-05-15    17667      5


    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    data have;
     informat start_datec $11.;
     input id start_datec date_start;
    cards4;
     1 2010-07-02 18445
     1 2011-08-17 18856
     1 2011-09-09 18879
     1 2011-11-14 18945

     2 2007-03-17 17242
     2 2007-06-29 17346
     2 2007-07-17 17364
     2 2007-08-14 17392
     2 2007-09-04 17413
     2 2008-02-18 17580
     2 2008-03-21 17612
     2 2008-04-15 17637
     2 2008-04-29 17651
     2 2008-05-15 17667
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    ;

    * SAS
    data want;
      retain id;
      set have;
       by id;
       if first.id then do;
          line=1;
          baseline_date = date_start;
       end;
       else if date_start - baseline_date > 60 then do;
          line + 1;
          baseline_date = date_start;
       end;
       retain baseline_date;
       drop baseline_date;
    run;quit;

    *WPS;
    %utl_submit_wps64('
    options set=R_HOME "C:/Program Files/R/R-3.3.1";
    libname wrk sas7bdat "%sysfunc(pathname(work))";
      data wrk.wantwps;
        retain id;
        set wrk.have;
         by id;
         if first.id then do;
            line=1;
            baseline_date = date_start;
         end;
         else if date_start - baseline_date > 60 then do;
            line + 1;
            baseline_date = date_start;
         end;
         retain baseline_date;
         drop baseline_date;
      run;quit;
    ');

