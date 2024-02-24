# utl-create-a-stored-datastep-program-with-sql-code-that-creates-a-dynamic-array-transpose
Transpose any matrix using a single stored sas datsatep program
    %let pgm=utl-create-a-stored-datastep-program-with-sql-code-that-creates-a-dynamic-array-transpose;

    Create a stored datastep program with sql code that creates a dynamic array transpose

    This is just a proof of concept. I don't think this should be used in production.

    Problem: Transpose any matrix using a single stored sas datsatep program

    I was ubable to create a dynamic array for easy transposing using dosubl.
    I don't think it is possible to create a stored program with dosubl that can
    can generated and execute dynamic array.

    github
    http://tinyurl.com/2w6xr6cu
    https://github.com/rogerjdeangelis/utl-create-a-stored-datastep-program-with-sql-code-that-creates-a-dynamic-array-transpose

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                 |                                                              |                       */
    /*        INPUT                    |       PROCESS                                                |        OUTPUT         */
    /*                                 |                                                              | (need to open         */
    /* SASUSER.MAT total obs=4         |                                                              |    c:/utl/ary.log)    */
    /*                                 |                                                              |                       */
    /*                 MPG_            |   data pgm=sto.want;                                         |                       */
    /*   MPG_CITY    HIGHWAY           |   run;                                                       |      TRANSPOSED       */
    /*                                 |                                                              |                       */
    /*      17          23             |                                                              |    17   24   22   20  */
    /*      24          31             |                                                              |    23   31   29   28  */
    /*      22          29             |                                                              |                       */
    /*      20          28             |                                                              |                       */
    /*                                 |                                                              |                       */
    /*                                 |                                                              |                       */
    /* SASUSER.MAT total obs=3         |   No need to recompile (just provide new dataset banes mat)  |      TRANSPOSED       */
    /*                                 |                                                              |                       */
    /*   Cr                            |   data pgm=sto.want;                                         |   293   3449   1624   */
    /*   At   Cr   Cr   Cr   Cr        |   run;                                                       |    66    835    457   */
    /*   Bat Hits Home Runs Rbi CrBB   |                                                              |     1     69     63   */
    /*                                 |                                                              |    30    321    224   */
    /*   293   66   1    30  29   14   |                                                              |    29    414    266   */
    /*  3449  835  69   321 414  375   |                                                              |    14    375    263   */
    /*  1624  457  63   224 266  263   |                                                              |                       */
    /*                                 |   COMPILED SINGLE DATASTEP (stored in d:/sto/want.sasybpgm)  |                       */
    /*                                 |   =========================================================  |                       */
    /*                                 |   %utlfkil(c:/utl/ary.sas);                                  |                       */
    /*                                 |                                                              |                       */
    /*                                 |   data want/ pgm=sto.want ;                                  |                       */
    /*                                 |                                                              |                       */
    /*                                 |     gen_code = dosubl('                                      |                       */
    /*                                 |       data tst;                                              |                       */
    /*                                 |          length catsas $4096;                                |                       */
    /*                                 |          set sashelp.vtable;                                 |                       */
    /*                                 |          if memname="MAT" and libname="SASUSER";             |                       */
    /*                                 |          catsas=catx(" "                                     |                       */
    /*                                 |            , "data _null_;                                 " |                       */
    /*                                 |            , "set  sasuser.mat  end=dne;                   " |                       */
    /*                                 |            , "array xs _numeric_;                          " |                       */
    /*                                 |            , "retain nrow",nobs,"ncol",nvar,";             " |                       */
    /*                                 |            , "array rowcol[",nobs,",",nvar,"] _temporary_; " |                       */
    /*                                 |            , "do col = 1 to ncol;                          " |                       */
    /*                                 |            , "  rowcol[_n_,col]=xs[col];                   " |                       */
    /*                                 |            , "end;                                         " |                       */
    /*                                 |            , "if dne then do;                              " |                       */
    /*                                 |            , "   do col=1 to ncol;                         " |                       */
    /*                                 |            , "     do row=1 to nrow;                       " |                       */
    /*                                 |            , "         put rowcol[row,col]  5. @;          " |                       */
    /*                                 |            , "      end;                                   " |                       */
    /*                                 |            , "      put /;                                 " |                       */
    /*                                 |            , "    end;                                     " |                       */
    /*                                 |            , "end;                                         " |                       */
    /*                                 |            , "run;quit;                                    " |                       */
    /*                                 |            );                                                |                       */
    /*                                 |          file "c:/utl/ary.sas";                              |                       */
    /*                                 |          put catsas;                                         |                       */
    /*                                 |          putlog catsas;                                      |                       */
    /*                                 |       run;quit;                                              |                       */
    /*                                 |       ');                                                    |                       */
    /*                                 |                                                              |                       */
    /*                                 |       call system('sas -sysin "c:/utl/ary.sas" -rsasuser');  |                       */
    /*                                 |                                                              |                       */
    /*                                 |   run;quit;                                                  |                       */
    /*                                 |                                                              |                       */
    /**************************************************************************************************************************/


    /*                   _
    (_)_ __  _ __  _   _| |_ ___
    | | `_ \| `_ \| | | | __/ __|
    | | | | | |_) | |_| | |_\__ \
    |_|_| |_| .__/ \__,_|\__|___/
            |_|
    */
    data sasuser.mat;
      set sashelp.cars
        (keep=mpg_city mpg_highway obs=4);
    run;quit;

    data sasuser.mat;
      set  sashelp.baseball
        (keep=cr: obs=3);
    run;quit;

    /*                         _ _        _   _     _
      ___ ___  _ __ ___  _ __ (_) | ___  | |_| |__ (_)___
     / __/ _ \| `_ ` _ \| `_ \| | |/ _ \ | __| `_ \| / __|
    | (_| (_) | | | | | | |_) | | |  __/ | |_| | | | \__ \
     \___\___/|_| |_| |_| .__/|_|_|\___|  \__|_| |_|_|___/
                        |_|
    */

    libname sto "d:/sto"; /*---- store compiled program ----*/

    %utlfkil(c:/utl/ary.sas);

    data want/ pgm=sto.want ;

      gen_code = dosubl('
        data tst;
           length catsas $4096;
           set sashelp.vtable;
           if memname="MAT" and libname="SASUSER";
           catsas=catx(" "
                  , "data _null_;                                 "
                  , "set  sasuser.mat  end=dne;                   "
                  , "array xs _numeric_;                          "
                  , "retain nrow",nobs,"ncol",nvar,";             "
                  , "array rowcol[",nobs,",",nvar,"] _temporary_; "
                  , "do col = 1 to ncol;                          "
                  , "  rowcol[_n_,col]=xs[col];                   "
                  , "end;                                         "
                  , "if dne then do;                              "
                  , "   do col=1 to ncol;                         "
                  , "     do row=1 to nrow;                       "
                  , "         put rowcol[row,col]  5. @;          "
                  , "      end;                                   "
                  , "      put /;                                 "
                  , "    end;                                     "
                  , "end;                                         "
                  , "run;quit;                                    "
                  );
           file "c:/utl/ary.sas";
           put catsas;
           putlog catsas;
        run;quit;
        ');

        call system('sas -sysin "c:/utl/ary.sas" -rsasuser');

    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                               INPUTS                                                                                   */
    /*                                                                                                                        */
    /*                                 |                                                                                      */
    /* SASUSER.MAT total obs=4         |  SASUSER.MAT total obs=3                                                             */
    /*                                 |                                                                                      */
    /*                 MPG_            |    Cr                                                                                */
    /*   MPG_CITY    HIGHWAY           |    At   Cr   Cr   Cr   Cr                                                            */
    /*                                 |    Bat Hits Home Runs Rbi CrBB                                                       */
    /*      17          23             |                                                                                      */
    /*      24          31             |    293   66   1    30  29   14                                                       */
    /*      22          29             |   3449  835  69   321 414  375                                                       */
    /*      20          28             |   1624  457  63   224 266  263                                                       */
    /*                                 |                                                                                      */
    /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    data sasuser.mat;
      set sashelp.cars
        (keep=mpg_city mpg_highway obs=4);
    run;quit;

    data pgm=sto.want;
    run;


    data sasuser.mat;
      set  sashelp.baseball
        (keep=cr: obs=3);
    run;quit;


    data pgm=sto.want;
    run;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* LOGS                                                                                                                       */
    /*                                                                                                                        */
    /* NOTE: SAS initialization used:                                                                                         */
    /*       real time           0.33 seconds                                                                                 */
    /*       cpu time            0.24 seconds                                                                                 */
    /*                                                                                                                        */
    /*   data _null_; set  sasuser.mat  end=dne; array xs _numeric_; retain nrow 4 ncol 2 ; array rowcol[ 4 , 2 ]             */
    /* ! _temporary_; do col = 1 to ncol; rowcol[_n_,col]=xs[col]; end; if dne then do; do col=1 to ncol; do row=1 to nrow;   */
    /* !  put rowcol[row,col]  5. @; end; put /; end; end; run;quit;                                                          */
    /*                                                                                                                        */
    /*    17   24   22   20                                                                                                   */
    /*    23   31   29   28                                                                                                   */
    /*                                                                                                                        */
    /* NOTE: There were 4 observations read from the data set SASUSER.MAT.                                                    */
    /* NOTE: DATA statement used (Total process time):                                                                        */
    /*       real time           0.01 seconds                                                                                 */
    /*       cpu time            0.03 seconds                                                                                 */
    /*                                                                                                                        */
    /*========================================================================================================================*/
    /*                                                                                                                        */
    /*   data _null_; set  sasuser.mat  end=dne; array xs _numeric_; retain nrow 3 ncol 6 ; array rowcol[ 3 , 6 ]             */
    /* ! _temporary_; do col = 1 to ncol; rowcol[_n_,col]=xs[col]; end; if dne then do; do col=1 to ncol; do row=1 to nrow;   */
    /* !  put rowcol[row,col]  5. @; end; put /; end; end; run;quit;                                                          */
    /*                                                                                                                        */
    /*   293 3449 1624                                                                                                        */
    /*    66  835  457                                                                                                        */
    /*     1   69   63                                                                                                        */
    /*    30  321  224                                                                                                        */
    /*    29  414  266                                                                                                        */
    /*    14  375  263                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* NOTE: There were 3 observations read from the data set SASUSER.MAT.                                                    */
    /* NOTE: DATA statement used (Total process time):                                                                        */
    /*       real time           0.00 seconds                                                                                 */
    /*       cpu time            0.01 seconds                                                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */

