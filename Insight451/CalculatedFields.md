# Insight451 Calculated Fields Repo
The following variables use a "left-join" from element_user + element_applications.  Then, we can compute:  

* [Funnel Stage](#funnel-stage)
* [ActiveTerm](#active-term)
* [Year to Year](#yoy)
* [Engagement Score](#engagement-score)
* [Seed Filters](#seed-filter)


# Funnel Stage 

Computes recruitment funnel from milestones.
```
IF (ISNULL([Milestone Admit Date]) AND 
    ISNULL([Milestone Application Submit Date]) AND 
    ISNULL([Milestone Application Start Date]) AND
    ISNULL([Milestone Deposit Date]) AND 
    ISNULL([Milestone Prospect Date]))
    AND
    not ISNULL([Milestone Created Date])
THEN "1-Suspect"
ELSEIF (ISNULL([Milestone Admit Date]) AND 
    ISNULL([Milestone Application Submit Date]) AND 
    ISNULL([Milestone Application Start Date]) AND
    ISNULL([Milestone Deposit Date]))
    AND
    not ISNULL([Milestone Prospect Date])
THEN "2-Prospect"
ELSEIF (ISNULL([Milestone Admit Date]) AND
    ISNULL([Milestone Deposit Date]))
    AND
    (not ISNULL([Milestone Application Start Date]) 
        OR not ISNULL([Milestone Application Complete Date]) 
        OR not ISNULL([Milestone Application Submit Date])) 
THEN "3-Applicants"
ELSEIF (ISNULL([Milestone Deposit Date])) AND 
    not ISNULL([Milestone Admit Date])
THEN "4-Admits"
ELSEIF
    not ISNULL([Milestone Deposit Date])
THEN "5-Deposit"
ELSEIF
    not ISNULL([Milestone Enroll Date])
THEN "6-Enrolled"
ELSEIF
    not ISNULL([Milestone Withdraw Date])
THEN "Other:Withdrawn/Melt"
ELSEIF
    not ISNULL([Milestone Waitlist Date])
THEN "Other:Waitlist"
ELSE "Other:NA"
END
```

# Active Term
Assigns a term
```
IFNULL([Term Name],[Intended Term Name])
```

# YoY%
Return % change from year-to-year
```
sum([Is In Current Year])/sum([Is In Prior Year])-1

```
[Is In Current Year]
```
IF [Submitted At] > [Sem start date (mm/dd)] AND  [Submitted At] <= [YoY (mm/dd) (copy)] AND [Term Name] == [Current Year Term ]
THEN 1
Else 0
END
```

[Is In Prior Year]
```
IF [Submitted At] > DATEADD('year',-1,[Sem start date (mm/dd)]) AND  
[Submitted At] <= DATEADD('year',-1,[YoY (mm/dd) (copy)])
AND [Term Name] == [Prior Year Term]
THEN 1
Else 0
END
```

# Engagement Score
Computes digital engagement score from email, SMS, forms, and logins
```

#Trait Computation = "engagementScore"
engagementScore = 0.20*emailScore + 0.20* smsScore+ 0.20*formSaved + 0.20*userLogin + 0.20*pageView

emailScore = 0.9*emailClicked/emailDelivered + 0.1*log(emailClicked)

smsScore = smsClicked/smsDelivered

formScore =0.1*(formSave/10)+0.9*(formTotal/5)
formSave = if (total_saves <=9) THEN total_saves
	if (total_saves >9) THEN "10"

formTotal = if (total_forms <=4) THEN total_forms
	if (total_forms >4) THEN "5"
 
userLogin= if (#dayssincelastlogin<=10) THEN "1" 
	if (#dayssincelastlogin<=30) THEN "0.75"
	if (#dayssincelastlogin<=60) then "0.5"
	if (#dayssincelastlogin>60) then "0.1"

pageView = if (#pageviews==1) THEN ".25" 
	if (#pageviews<=3) ) THEN "0.5"
	if (#pageviews<10)  THEN "0.75"
	if (#pageviews>=10)  THEN "1"


#Validation/Truncation
if emailScore >1 then emailScore == 1
if smsScore >1 then smsScore == 1
if emailUnsubscribed == TRUE then emailScore == 0
if emailUnsubscribed == TRUE then emailScore == 0

#Labeling
If engagementScore ==0 then "DORMANT"
If engagementScore <0.33 then "LURKER"
If engagementScore >=0.33 then "FAN"

```

# Seed Filter
Removes seeds and test users such as "xxx@spark451.com"
```
IF CONTAINS(LOWER([Email]),"@spark451.com") or 
	CONTAINS(LOWER([Email]),"test") or 
	LOWER([First Source Code]) = "seed" 
THEN "Y" 
ELSE "N" 
END

```
