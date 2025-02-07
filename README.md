# utl-r-sql-vs-dplyr-benchmarks-selecting-means-by-group-for-one-hundrend-thousand-rows
r sql vs dplyr benchmarks selecting means by group for one hundrend thousand rows 
    %let pgm=utl-r-sql-vs-dplyr-benchmarks-selecting-means-by-group-for-one-hundrend-thousand-rows;

    %stop_submission;

    r sql vs dplyr benchmarks selecting means by group for one hundrend thousand rows

    The post provides ways to speed-up sql processing in r (may work in python?)

    github
    https://tinyurl.com/y4utmf53
    https://github.com/rogerjdeangelis/utl-r-sql-vs-dplyr-benchmarks-selecting-means-by-group-for-one-hundrend-thousand-rows

    SOAPBOX ON
      Although dplyr is faster,  does it really matter. Both are sufficient;y fast for 100,000 rows
      I suspect quite a few analyses involve less than 100,000 rows(clinical)
      In memory languages do have strong points, but perhaps not big data?
    SOAPBOX OFF

       OVERVIEW

       R SQLITE IN MEMORY

        dplyr   11.2ms
        sqlite  24.1ms

       R SQLDF DB FILE

        dplyr   13.6ms
        sqldf   75.5ms


      BENCHMARKS (DOES IT REALLY MATTER)

       1 R SQLITE IN MEMORY DATABASE VS DPLYR

         dplyr       11.2
         sqlite      24.1 (NOTE I SORTED AND INDEXED THE HAVE TABLE)

         expression      min    median  itr/sec mem_alloc `gc/sec` n_itr  n_gc total_time
         dplyr          11ms   11.2ms      89.2    6.23MB        0    10     0      112ms
         sqlite         23.7ms 23.8ms      41.5    7.01KB        0    21     0      506ms


       2 R SQLDF (REDUCING THE TIME TO CONVERT R DATAFRAME TO SQLITE TABLE)

         (I created a sqlite table and stored it in apermanent file
          and then coputed the means using the sqlite table.
          This elimates the costly process od converting an r dataframe to a sqlite table.

         dplyr 13.6ms
         sqldf 75.5ms (PRE PROCESSED DATAFRAME INTO A SQLITE TABLE)

         expression   min    median itr/sec mem_alloc `gc/sec` n_itr  n_gc total_time
         dplyr       12.6ms 13.5ms    73.8     6.5MB     8.20     9     1      122ms
         sqldf       72.8ms 73.6ms    13.1    10.1KB        0    11     0      841ms


    /**************************************************************************************************************************/
    /*                            |                                         |                                                 */
    /*         INPUT              |    PROCESS                              |     OUTPUT                                      */
    /*         =====              |    =======                              |     ======                                      */
    /*      (30 GROUPS)           |                                         |                                                 */
    /*                            |                                         |  dplyr   11.2ms                                 */
    /*        A B C D  GRP        | 1 R SQLITE IN MEMORY DATABASE VS DPLYR  |  sqlite  24.1ms                                 */
    /*                            |   (see below)                           |                                                 */
    /*      1 3 3 3 3   21        |                                         |                                                 */
    /*      2 3 1 5 4   30        | 2 R SQLDF (PREPROCESS DATAFRAME         |  dplyr   13.6ms                                 */
    /*      3 2 4 2 1   11        |   INTO A SQLITE PEMANENT FILE           |  sqldf   75.5ms                                 */
    /*      4 2 4 1 1   25        |   BEFORE SUMMARIZATION (SEE BELOW)      |                                                 */
    /*      5 3 5 1 2   17        |                                         |  GRP    AVGA     AVGB     AVGC     AVGD         */
    /*      6 5 3 3 5   12        |                                         |                                                 */
    /* ..                         |                                         |    1 3.057202 2.993546 2.980346 3.017014        */
    /* 99,998 3 2 4 2 1 12        |                                         |    2 2.962401 3.013341 2.999697 2.979381        */
    /* 99,999 5 3 3 5 3 28        |                                         |    3 2.982187 3.031327 3.035319 2.990172        */
    /* 00,000 2 3 1 5 4 13        |                                         |    4 3.020348 3.016809 3.000590 2.983486        */
    /*                            |                                         |    5 2.966566 3.025000 3.034036 3.010843        */
    /*                            |                                         |    6 2.975146 3.004678 3.000000 2.994152        */
    /*                            |                                         |    7 2.999696 2.987215 3.069406 2.987215        */
    /*                            |                                         |    8 3.004853 3.021535 3.027298 3.005763        */
    /*                            |                                         |    9 2.978742 3.028934 2.958665 2.988781        */
    /*                            |                                         |   10 2.958196 3.050463 3.027172 3.011944        */
    /*                            |                                         |   11 3.005988 3.022455 2.996108 2.979940        */
    /*                            |                                         |   12 3.011799 2.986726 3.009735 2.979351        */
    /*                            |                                         |   13 3.031204 2.962853 2.960773 3.036553        */
    /*                            |                                         |   14 3.028223 2.998217 2.978313 2.975639        */
    /*                            |                                         |   15 3.000000 2.988615 2.993846 2.989846        */
    /*                            |                                         |   16 2.985829 2.990450 2.959335 2.969501        */
    /*                            |                                         |   17 3.006263 3.025052 3.000895 2.996421        */
    /*                            |                                         |   18 2.974250 2.960921 3.039382 2.980612        */
    /*                            |                                         |   19 3.026405 3.007983 3.003991 3.002763        */
    /*                            |                                         |   20 2.982240 2.989765 3.021975 3.007827        */
    /*                            |                                         |   21 2.989590 3.031537 2.983466 2.991121        */
    /*                            |                                         |   22 3.018074 3.029916 2.978810 3.026800        */
    /*                            |                                         |   23 3.000891 3.005346 2.971191 2.991981        */
    /*                            |                                         |   24 2.990566 2.994693 3.026238 2.992925        */
    /*                            |                                         |   25 2.988717 3.019299 2.981888 2.981888        */
    /*                            |                                         |   26 2.999135 2.988754 3.019319 3.004325        */
    /*                            |                                         |   27 2.990616 3.037537 3.044282 2.989736        */
    /*                            |                                         |   28 3.001198 2.999102 2.988024 2.966168        */
    /*                            |                                         |   29 2.986679 3.008174 3.023615 2.990009        */
    /*                            |                                         |   30 2.992061 3.018321 2.989008 2.981374        */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /*                            |                                         |                                                 */
    /**************************************************************************************************************************/

    /*             _ _ _         _                                                        _ _
    / |  ___  __ _| (_) |_ ___  (_)_ __   _ __ ___   ___ _ __ ___   ___  _ __ _   _    __| | |__
    | | / __|/ _` | | | __/ _ \ | | `_ \ | `_ ` _ \ / _ \ `_ ` _ \ / _ \| `__| | | |  / _` | `_ \
    | | \__ \ (_| | | | ||  __/ | | | | || | | | | |  __/ | | | | | (_) | |  | |_| | | (_| | |_) |
    |_| |___/\__, |_|_|\__\___| |_|_| |_||_| |_| |_|\___|_| |_| |_|\___/|_|   \__, |  \__,_|_.__/
                |_|                                                           |___/
    */

    %utl_rbeginx;
    parmcards4;
    library(RSQLite)
    library(DBI)
    library(haven)
    library(bench)
    library(dplyr)
    source("c:/oto/fn_tosas9x.R")
    n=100000;
    set.seed(123)
    have <- data.frame(
        a = sample(1:5, n, replace = TRUE),
        b = sample(1:5, n, replace = TRUE),
        c = sample(1:5, n, replace = TRUE),
        d = sample(1:5, n, replace = TRUE),
        grp = sample(1:30, n, replace = TRUE)
     )
    mark(
      dplyr = have %>% group_by(grp) %>% summarise(across(everything(), list(mean)))
       ,iterations=10)

     con <- dbConnect(RSQLite::SQLite(), ":memory:")

     dbWriteTable(conn = con, name = "have", value = have)
     dbExecute(con,
        "CREATE
             table havsrt as
         select
            *
         from
            have
         order
            by grp
         ");

     dbExecute(con,
        "CREATE
            INDEX grp
         on
            havsrt(grp)")

     mark(
       result <- dbGetQuery(con,
        "select
            grp
           ,avg(a) as avga
           ,avg(b) as avgb
           ,avg(c) as avgc
           ,avg(d) as avgd
         from
            havsrt
         group
            by grp")
         )

     result
     dbDisconnect(con)
    ;;;;
    %utl_rendx;

    /*___              _     _  __    __ _ _            _ _
    |___ \   ___  __ _| | __| |/ _|  / _(_) | ___    __| | |__
      __) | / __|/ _` | |/ _` | |_  | |_| | |/ _ \  / _` | `_ \
     / __/  \__ \ (_| | | (_| |  _| |  _| | |  __/ | (_| | |_) |
    |_____| |___/\__, |_|\__,_|_|   |_| |_|_|\___|  \__,_|_.__/
                    |_|
    */


    %utlfkil(d:/sql/havsql);

    %utl_rbeginx;
    parmcards4;
    library(sqldf)
    library(bench)
    library(dplyr)
    n=100000;
    set.seed(123)
    have <- data.frame(
        a = sample(1:5, n, replace = TRUE),
        b = sample(1:5, n, replace = TRUE),
        c = sample(1:5, n, replace = TRUE),
        d = sample(1:5, n, replace = TRUE),
        grp = sample(1:30, n, replace = TRUE)
    )
    sqldf("attach 'd:/sql/havsql' as new")


    mark(
    want<-have %>% group_by(grp) %>%
      summarise_at(.vars = vars(a,b,c,d),
                     .funs = c(mean="mean"))
    ,iterations=10)

    sqldf("create table havsql as select * from have", dbname = 'd:/sql/havsql')

    mark(
    want<-sqldf('
     select
        grp
       ,avg(a) as avga
       ,avg(b) as avgb
       ,avg(c) as avgc
       ,avg(d) as avgd
     from
        havsql
     group
        by grp'
     ,dbname = "d:/sql/havsql"),iterations=11)

    mark(
    want<-sqldf('
     select
        grp
       ,avg(a) as avga
       ,avg(b) as avgb
       ,avg(c) as avgc
       ,avg(d) as avgd
     from
        have
     group
        by grp')
    ,iterations=11)
    want
    ;;;;
    %utl_rendx;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
