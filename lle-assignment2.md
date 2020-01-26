# Picking a Question

While designing a sales contest, the contest planning committee was
wondering what an appropriate scale would be for awarding points. This
became the focus of my work for this project. I needed to answer: During
the contest period last year, how many meetings, calls and emails do
sales colleagues normally have each week?

# Getting the Data

This data is available through Salesforce reporting but there are
limitations to how the information can be retrieved. In short, each
activity (meeting, call, email) for each week (8 weeks total) is
reportable, but requires 24 reports (one activity per week at a time).
All reports were downloaded as excel documents, edited to remove
headings and extra columns, then saved as .csv.

# Transformation and Rearrangements

Using R, I combined my .csv files to create the appropriate data tables.
Using R was helpful because full\_join() automatically matched up the
names of the sales colleagues across all of the tables, putting NA’s
where the person did not have any data.

I now had three data.tables, one for Meetings, another for Calls and a
third for Emails. Each table has a column with the person’s name,
followed by 8 columnns with the activity counts for each person for 8
weeks. I used gather() to create long data tables. The new data tables
have three columns: the person name, the week, and the activity count.

# Visualization

At our follow up contest planning meeting I brought 6 graphs. I started
with 3 histograms with 8-week totals for each activity. These three
graphs helped the team choose the thresholds for scoring points. I
follwed up by using facet\_wrap() to tease out the weekly detail. This
allowed the team to apply their proposed scoring rubric and predict how
many people would score points each week.

I tried many kinds of graphs before ultimately settling on the simple
histogram. I tried box and whisker, but actually found the information
was more valuable presented as text next to the histograms. I also tried
geom\_density(), geom\_dotplot(), geom\_freqpoly() and geom\_violin(),
Ultimately, I felt the simple histogram was the most intuitive option.

My final document, shared with the contest planning committee, is
enclosed.

# Bring the Data In

``` r
Meet1 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/1_Meetings_Last_Week.csv")
Meet2 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/2_Meetings_Last_Week.csv")
Meet3 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/3_Meetings_Last_Week.csv")
Meet4 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/4_Meetings_Last_Week.csv")
Meet5 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/5_Meetings_Last_Week.csv")
Meet6 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/6_Meetings_Last_Week.csv")
Meet7 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/7_Meetings_Last_Week.csv")
Meet8 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/8_Meetings_Last_Week.csv")

Call1 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/1_Calls_Last_Week.csv")
Call2 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/2_Calls_Last_Week.csv")
Call3 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/3_Calls_Last_Week.csv")
Call4 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/4_Calls_Last_Week.csv")
Call5 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/5_Calls_Last_Week.csv")
Call6 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/6_Calls_Last_Week.csv")
Call7 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/7_Calls_Last_Week.csv")
Call8 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/8_Calls_Last_Week.csv")

Email1 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/1_Emails_Last_Week.csv")
Email2 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/2_Emails_Last_Week.csv")
Email3 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/3_Emails_Last_Week.csv")
Email4 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/4_Emails_Last_Week.csv")
Email5 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/5_Emails_Last_Week.csv")
Email6 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/6_Emails_Last_Week.csv")
Email7 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/7_Emails_Last_Week.csv")
Email8 <- fread("C:/Users/le/Desktop/GDAT515/lle-assignment3/Activities/8_Emails_Last_Week.csv")
```

# Creating one Dataset

``` r
Meetings <- join_all(list(Meet1, Meet2, Meet3, Meet4, Meet5, Meet6, Meet7, Meet8), by = "Assigned", type = "full")
View(Meetings)

Calls <- join_all(list(Call1, Call2, Call3, Call4, Call5, Call6, Call7, Call8), by = "Assigned", type = "full")
Calls <- select(Calls, Assigned, Week1Calls, Week2Calls, Week3Calls, Week4Calls, Week5Calls, Week6Calls, Week7Calls, Week8Calls)
View(Calls)

Emails <- join_all(list(Email1, Email2, Email3, Email4, Email5, Email6, Email7, Email8), by = "Assigned", type = "full")
Emails <- select(Emails, Assigned, Week1Emails, Week2Emails, Week3Emails, Week4Emails, Week5Emails, Week6Emails, Week7Emails, Week8Emails)
View(Emails)

Activities <- join_all(list(Meetings, Calls, Emails), by = "Assigned", type = "full")
View(Activities)
```

# EDA Meetings with ggplot

``` r
longMeetings <- gather(data = Meetings, "Week1Meeting", "Week2Meeting", "Week3Meeting", "Week4Meeting", "Week5Meeting", "Week6Meeting", "Week7Meeting", "Week8Meeting", key = "week", value = "Meets")
View(longMeetings)

str(longMeetings)
```

    ## 'data.frame':    544 obs. of  3 variables:
    ##  $ Assigned: chr  "Tony Culiano" "Shawn Corrigan" "Zachary Gibbs" "Tim Coulter" ...
    ##  $ week    : chr  "Week1Meeting" "Week1Meeting" "Week1Meeting" "Week1Meeting" ...
    ##  $ Meets   : int  6 6 5 5 5 4 4 4 4 4 ...

``` r
ggplot(longMeetings, aes(Meets))+
  geom_bar()+
  scale_y_continuous(breaks = seq(0, 130, 10))+
  xlim(c("1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15"))+
  labs(title = "How many Meetings are AVPs completing each week?", subtitle = "Data from Feb - Mar 2019 (SPA79 - March Madness)", x = "X = Number of Meetings the AVP had for the Week", y = "Frequency that Meeting Count = X")+
  theme_grey(base_size = 20)
```

    ## Warning: Removed 165 rows containing non-finite values (stat_count).

![](lle-assignment3_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

``` r
ggplot(longMeetings, aes(Meets))+
  geom_bar()+
  facet_wrap(vars(week))+
  scale_y_continuous(breaks = seq(0, 20, 5))+
  xlim(c("1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15"))+
  labs(title = "How many Meetings are AVPs completing each week?", subtitle = "Data from Feb - Mar 2019 (SPA79 - March Madness)", x = "X = Number of Meetings the AVP had for the Week", y = "Frequency that Meeting Count = X")+
  theme_grey(base_size = 20)
```

    ## Warning: Removed 165 rows containing non-finite values (stat_count).

![](lle-assignment3_files/figure-gfm/unnamed-chunk-1-2.png)<!-- -->

``` r
summary(longMeetings)
```

    ##    Assigned             week               Meets       
    ##  Length:544         Length:544         Min.   : 1.000  
    ##  Class :character   Class :character   1st Qu.: 1.000  
    ##  Mode  :character   Mode  :character   Median : 2.000  
    ##                                        Mean   : 2.844  
    ##                                        3rd Qu.: 4.000  
    ##                                        Max.   :15.000  
    ##                                        NA's   :165

# EDA Calls with ggPlot

``` r
longCalls <- gather(data = Calls, "Week1Calls", "Week2Calls", "Week3Calls", "Week4Calls", "Week5Calls", "Week6Calls", "Week7Calls", "Week8Calls", key = "week", value = "Calls")
View(longCalls)

ggplot(longCalls, aes(x = Calls))+
  geom_bar()+
  scale_y_continuous(breaks=seq(0, 50, 5))+
  scale_x_continuous(breaks = seq(0, 50, 5))+
  labs(title = "How many Successful Calls are AVPs completing each week?", subtitle = "Data from Feb - Mar 2019 (SPA79 - March Madness)", x = "X = Number of Successful Calls the AVP had for the Week", y = "Frequency that Call Count = X")+
   theme_grey(base_size = 20)
```

    ## Warning: Removed 179 rows containing non-finite values (stat_count).

![](lle-assignment3_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
ggplot(longCalls, aes(x = Calls))+
  geom_bar()+
  facet_wrap(vars(week))+
  scale_y_continuous(breaks=seq(0, 50, 2))+
  scale_x_continuous(breaks = seq(0, 50, 5))+
  labs(title = "How many Successful Calls are AVPs completing each week?", subtitle = "Data from Feb - Mar 2019 (SPA79 - March Madness)", x = "X = Number of Successful Calls the AVP had for the Week", y = "Frequency that Call Count = X")+
   theme_grey(base_size = 20)
```

    ## Warning: Removed 179 rows containing non-finite values (stat_count).

![](lle-assignment3_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

``` r
summary(longCalls)
```

    ##    Assigned             week               Calls      
    ##  Length:704         Length:704         Min.   : 1.00  
    ##  Class :character   Class :character   1st Qu.: 5.00  
    ##  Mode  :character   Mode  :character   Median :10.00  
    ##                                        Mean   :10.86  
    ##                                        3rd Qu.:15.00  
    ##                                        Max.   :35.00  
    ##                                        NA's   :179

``` r
longEmails <- gather(data = Emails, "Week1Emails", "Week2Emails", "Week3Emails", "Week4Emails", "Week5Emails", "Week6Emails", "Week7Emails", "Week8Emails", key = "week", value = "Emails")
View(longEmails)

ggplot(data = longEmails, mapping = aes (x = week, y = Emails))+
  geom_boxplot()
```

    ## Warning: Removed 2095 rows containing non-finite values (stat_boxplot).

![](lle-assignment3_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
ggplot(longEmails, aes(Emails))+
  geom_bar()+
  scale_x_continuous(breaks = seq(0, 100, 10))+
  labs(title = "How many Successful Emails are AVPs receiving each week?", subtitle = "Data from Feb - Mar 2019 (SPA79 - March Madness)", x = "X = Number of Successful Emails the AVP had for the Week", y = "Frequency that Emails Count = X")+
   theme_grey(base_size = 20)
```

    ## Warning: Removed 2095 rows containing non-finite values (stat_count).

![](lle-assignment3_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

``` r
ggplot(longEmails, aes(Emails))+
  geom_bar()+
  facet_wrap(vars(week))+
  scale_x_continuous(breaks = seq(0, 100, 10))+
  labs(title = "How many Successful Emails are AVPs receiving each week?", subtitle = "Data from Feb - Mar 2019 (SPA79 - March Madness)", x = "X = Number of Successful Emails the AVP had for the Week", y = "Frequency that Emails Count = X")+
   theme_grey(base_size = 20)
```

    ## Warning: Removed 2095 rows containing non-finite values (stat_count).

![](lle-assignment3_files/figure-gfm/unnamed-chunk-3-3.png)<!-- -->

``` r
summary(longEmails)
```

    ##    Assigned             week               Emails     
    ##  Length:2632        Length:2632        Min.   : 1.00  
    ##  Class :character   Class :character   1st Qu.:11.00  
    ##  Mode  :character   Mode  :character   Median :20.00  
    ##                                        Mean   :23.21  
    ##                                        3rd Qu.:32.00  
    ##                                        Max.   :87.00  
    ##                                        NA's   :2095

# Finding the total and average number of meetings per person.

The team asked me to help them stack rank their peers to build equal
teams and my manager asked to have the data available in excel. That is
below.

``` r
Meetings2 <- as.data.frame(Meetings)

Meetings2[is.na(Meetings2)] <-0

View(Meetings2)

#Meetings2 is a dataframe where all zeros are now

MeetingsAVP <- Meetings2 %>%
  mutate(MeetingTotal = Week1Meeting + Week2Meeting + Week3Meeting + Week4Meeting + Week5Meeting + Week6Meeting + Week7Meeting + Week8Meeting) %>%
  mutate(MeetAvg = MeetingTotal/8)

View(MeetingsAVP)
```

# Finding the total and average number of calls per person.

``` r
Calls2 <- as.data.frame(Calls)

Calls2[is.na(Calls2)] <-0

View(Calls2)

#Meetings2 is a dataframe where all zeros are now

CallsAVP <- Calls2 %>%
  mutate(CallsTotal = Week1Calls + Week2Calls + Week3Calls + Week4Calls + Week5Calls + Week6Calls + Week7Calls + Week8Calls) %>%
  mutate(CallAvg = CallsTotal/8)

View(CallsAVP)
```

# Finding the total and average number of emails per person.

``` r
Emails2 <- as.data.frame(Emails)

Emails2[is.na(Emails2)] <-0

View(Emails2)

#Meetings2 is a dataframe where all zeros are now

EmailsAVP <- Emails2 %>%
  mutate(EmailTotal = Week1Emails + Week2Emails + Week3Emails + Week4Emails + Week5Emails + Week6Emails + Week7Emails + Week8Emails) %>%
  mutate(EmailAvg = EmailTotal/8)

View(EmailsAVP)
```

# Creating a dataframe with just Assigned and Totals for 8 Weeks, writing it to .csv to open in excel

``` r
AVPtotals <- join_all(list(MeetingsAVP, CallsAVP, EmailsAVP), by = "Assigned", type = "full")

AVPtotals <- AVPtotals %>%
  select(Assigned,MeetingTotal, CallsTotal, EmailTotal) %>%
  slice(1:44,46:72)%>%
  arrange(Assigned)

View(AVPtotals)

write.csv(AVPtotals,"AVPtotals.csv")
```
