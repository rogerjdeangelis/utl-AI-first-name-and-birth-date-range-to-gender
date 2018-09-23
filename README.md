# utl-AI-first-name-and-birth-date-range-to-gender
First name and birth date range to gender.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    First name and birth date range to gender

    github
    https://tinyurl.com/y83bdyat
    https://github.com/rogerjdeangelis/utl-AI-first-name-and-birth-date-range-to-gender

    see SAS  Forum
    https://tinyurl.com/yb2938ll
    https://communities.sas.com/t5/Coding-on-SAS-Viya/Data-Studio-Gender-Analysis-only-U-values/m-p/498256

    * Could not find a comparable Python packages, not interested in a Web API.

    Installation using R command window
    In dos R window
    > install.packages("gender")
    > library(gender)
    > install_genderdata_package()   /* imnstalls the database */

    utl_rens macro on end

    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories


    INPUT
    =====

     SD1.HAVE total obs=7

       NAME

       roger
       james
       mary
       kate
       jane
       janet
       mike


    EXAMPLE OUTPUT
    --------------

     WORK.WANT_SEX total obs=7

                 PROBABILITY_    PROBABILITY_
        NAME         MALE          FEMALE      GENDER    YEAR_MIN    YEAR_MAX

        jane        0.0029         0.9971      female      1930        2012
        janet       0.0027         0.9973      female      1930        2012
        kate        0.0012         0.9988      female      1930        2012
        mary        0.0036         0.9964      female      1930        2012

        james       0.9955         0.0045      male        1930        2012
        mike        0.9965         0.0035      male        1930        2012
        roger       0.9962         0.0038      male        1930        2012


    PROCESS
    =======

    %utl_submit_r64('
    library(SASxport);
    library(dplyr);
    library(gender);
    library(haven);
    library(Hmisc);
    have<-read_sas("d:/sd1/have.sas7bdat");
    want<-as.data.frame(gender(have$NAME, method = "ssa", years = c(1930, 2012)));
    for (i in seq_along(want)) {
       label(want[,i])<- colnames(want)[i];
    };
    write.xport(want,file="d:/xpt/rxpt.xpt");
    ');

    libname xpt xport "d:/xpt/rxpt.xpt";

    proc contents data=xpt._all_;
    run;quit;

    data want_sex; /* cannot use name want */

      %utl_rens(xpt.want);
      set want;

    run;quit;


    OUTPUT
    ======

    see above


    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
     input name$;
    cards4;
    roger
    james
    mary
    kate
    jane
    janet
    mike
    ;;;;
    run;quit;

    *
     _ __ ___   __ _  ___ _ __ ___
    | '_ ` _ \ / _` |/ __| '__/ _ \
    | | | | | | (_| | (__| | | (_) |
    |_| |_| |_|\__,_|\___|_|  \___/

    ;

    %macro utl_rens(dsn)/des="use with R SASXport for long variable names";

      if _n_=0 then do;
        rc=%sysfunc(dosubl('

            data __ren001;
               set &dsn(obs=1);
            run;quit;

            proc transpose data=__ren001 out=__ren002(drop=col1);
              var _all_;
            run;quit;

            proc sql;
              select
                catx(' ',_name_,"as",lbl) into :rens separated by ","
              from
                (
                 select
                    _name_
                   ,case
                        when (_label_ = ' ') then _name_
                        else _label_
                    end as lbl
                 from
                    __ren002
                )
           ;quit;

            proc sql;
               create
                   view %scan(&dsn,2,'.')  as
               select
                   &rens
               from
                   &dsn.
            ;quit;
        '));
        drop rc;
      end;

    %mend utl_rens;


