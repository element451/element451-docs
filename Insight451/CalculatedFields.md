# Insight451 Calculated Fields Repo
The following variables use a "left-join" from element_user + element_applications.  Then, we can compute:  

* [Funnel Stage](#funnel-stage)
* [ActiveTerm](#active-term)
* [Year to Year](#yoy)
* [Engagement Score](#engagement-score)
* [Seed Filters](#seed-filter)
* [EPS Territories](#eps-territories)


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

# EPS Territories
```
IF [Address Home Eps Code] = "AK 0" THEN "Alaska - Miscellaneous"
ELSEIF [Address Home Eps Code] = "AK 1" THEN "Anchorage, Kenai, & Mat-su District"
ELSEIF [Address Home Eps Code] = "AK 2" THEN "Greater Alaska"
ELSEIF [Address Home Eps Code] = "AL 0" THEN "Alabama - Miscellaneous"
ELSEIF [Address Home Eps Code] = "AL 1" THEN "Birmingham & Tuscaloosa"
ELSEIF [Address Home Eps Code] = "AL 2" THEN "Huntsville & Florence"
ELSEIF [Address Home Eps Code] = "AL 3" THEN "Mobile"
ELSEIF [Address Home Eps Code] = "AL 4" THEN "Montgomery"
ELSEIF [Address Home Eps Code] = "AR 0" THEN "Arkansas - Miscellaneous"
ELSEIF [Address Home Eps Code] = "AR 1" THEN "Little Rock & Southern Arkansas"
ELSEIF [Address Home Eps Code] = "AR 2" THEN "Northern Arkansas"
ELSEIF [Address Home Eps Code] = "AZ 0" THEN "Arizona - Miscellaneous"
ELSEIF [Address Home Eps Code] = "AZ 1" THEN "Phoenix"
ELSEIF [Address Home Eps Code] = "AZ 2" THEN "Tucson"
ELSEIF [Address Home Eps Code] = "AZ 3" THEN "Northern Arizona"
ELSEIF [Address Home Eps Code] = "CA 0" THEN "California - Miscellaneous"
ELSEIF [Address Home Eps Code] = "CA 1" THEN "Far Northern California"
ELSEIF [Address Home Eps Code] = "CA 2" THEN "Valley of the Moon"
ELSEIF [Address Home Eps Code] = "CA 3" THEN "Sacramento Co"
ELSEIF [Address Home Eps Code] = "CA 4" THEN "Marin Co"
ELSEIF [Address Home Eps Code] = "CA 5" THEN "San Francisco Co"
ELSEIF [Address Home Eps Code] = "CA 6" THEN "Contra Costa Co"
ELSEIF [Address Home Eps Code] = "CA 7" THEN "City of Oakland"
ELSEIF [Address Home Eps Code] = "CA 8" THEN "Alameda Co (w/o Oakland)"
ELSEIF [Address Home Eps Code] = "CA 9" THEN "San Mateo Co"
ELSEIF [Address Home Eps Code] = "CA10" THEN "City of San Jose"
ELSEIF [Address Home Eps Code] = "CA11" THEN "Santa Clara Co (excluding San Jose)"
ELSEIF [Address Home Eps Code] = "CA12" THEN "Central Coast"
ELSEIF [Address Home Eps Code] = "CA13" THEN "Santa Barbara & West Ventura Co"
ELSEIF [Address Home Eps Code] = "CA14" THEN "San Fernando Valley (West)"
ELSEIF [Address Home Eps Code] = "CA15" THEN "San Fernando Valley (East)"
ELSEIF [Address Home Eps Code] = "CA16" THEN "Glendale and Pasadena"
ELSEIF [Address Home Eps Code] = "CA17" THEN "West Los Angeles & West Beach"
ELSEIF [Address Home Eps Code] = "CA18" THEN "Hollywood & Wilshire"
ELSEIF [Address Home Eps Code] = "CA19" THEN "East Los Angeles"
ELSEIF [Address Home Eps Code] = "CA20" THEN "South Bay"
ELSEIF [Address Home Eps Code] = "CA21" THEN "South & South Central Los Angeles"
ELSEIF [Address Home Eps Code] = "CA22" THEN "Long Beach"
ELSEIF [Address Home Eps Code] = "CA23" THEN "Covina & West Covina"
ELSEIF [Address Home Eps Code] = "CA24" THEN "Whittier & North Orange Co"
ELSEIF [Address Home Eps Code] = "CA25" THEN "Anaheim"
ELSEIF [Address Home Eps Code] = "CA26" THEN "Santa Ana"
ELSEIF [Address Home Eps Code] = "CA27" THEN "Riverside, San Bernardino, & Ontario"
ELSEIF [Address Home Eps Code] = "CA28" THEN "South Orange Co"
ELSEIF [Address Home Eps Code] = "CA29" THEN "North San Diego Co (w/o San Diego)"
ELSEIF [Address Home Eps Code] = "CA30" THEN "South San Diego Co (w/o San Diego)"
ELSEIF [Address Home Eps Code] = "CA31" THEN "City of San Diego"
ELSEIF [Address Home Eps Code] = "CA32" THEN "Central Valley--North"
ELSEIF [Address Home Eps Code] = "CA33" THEN "Central Valley--South"
ELSEIF [Address Home Eps Code] = "CA34" THEN "Greater Imperial Valley"
ELSEIF [Address Home Eps Code] = "CO 0" THEN "Colorado - Miscellaneous"
ELSEIF [Address Home Eps Code] = "CO 1" THEN "Colorado Springs & Southeastern Colorado"
ELSEIF [Address Home Eps Code] = "CO 2" THEN "Metro Denver & Northeastern Colorado"
ELSEIF [Address Home Eps Code] = "CO 3" THEN "Mountain & Western Colorado"
ELSEIF [Address Home Eps Code] = "CT 0" THEN "Connecticut - Miscellaneous"
ELSEIF [Address Home Eps Code] = "CT 1" THEN "New London & Windham Co"
ELSEIF [Address Home Eps Code] = "CT 2" THEN "New Haven & Middlesex Co"
ELSEIF [Address Home Eps Code] = "CT 3" THEN "Fairfield Co"
ELSEIF [Address Home Eps Code] = "CT 4" THEN "Waterbury & Litchfield Co"
ELSEIF [Address Home Eps Code] = "CT 5" THEN "Hartford & Tolland Co"
ELSEIF [Address Home Eps Code] = "DC 0" THEN "District of Columbia - Miscellaneous"
ELSEIF [Address Home Eps Code] = "DC 1" THEN "District of Columbia"
ELSEIF [Address Home Eps Code] = "DE 0" THEN "Delaware - Miscellaneous"
ELSEIF [Address Home Eps Code] = "DE 1" THEN "New Castle Co"
ELSEIF [Address Home Eps Code] = "DE 2" THEN "Kent & Sussex Co"
ELSEIF [Address Home Eps Code] = "FL 0" THEN "Florida - Miscellaneous"
ELSEIF [Address Home Eps Code] = "FL 1" THEN "Panhandle"
ELSEIF [Address Home Eps Code] = "FL 2" THEN "Crown"
ELSEIF [Address Home Eps Code] = "FL 3" THEN "East Central Florida"
ELSEIF [Address Home Eps Code] = "FL 4" THEN "West Central Florida"
ELSEIF [Address Home Eps Code] = "FL 5" THEN "Broward, Martin, & Palm Beach Co"
ELSEIF [Address Home Eps Code] = "FL 6" THEN "Dade Co"
ELSEIF [Address Home Eps Code] = "FL 7" THEN "Collier, Hendry, & Monroe Co"
ELSEIF [Address Home Eps Code] = "GA 0" THEN "Georgia - Miscellaneous"
ELSEIF [Address Home Eps Code] = "GA 1" THEN "Cherokee, Cobb, & Douglas Co"
ELSEIF [Address Home Eps Code] = "GA 2" THEN "Fulton Co"
ELSEIF [Address Home Eps Code] = "GA 3" THEN "DeKalb & Gwinnett Co"
ELSEIF [Address Home Eps Code] = "GA 4" THEN "Clayton, Fayette, Henry, & Rockdale Co"
ELSEIF [Address Home Eps Code] = "GA 5" THEN "Northeast Georgia"
ELSEIF [Address Home Eps Code] = "GA 6" THEN "Southeast Georgia"
ELSEIF [Address Home Eps Code] = "GA 7" THEN "Southwest Georgia"
ELSEIF [Address Home Eps Code] = "GA 8" THEN "Northwest Georgia"
ELSEIF [Address Home Eps Code] = "HI 0" THEN "Hawaii - Miscellaneous"
ELSEIF [Address Home Eps Code] = "HI 1" THEN "Island of Oahu"
ELSEIF [Address Home Eps Code] = "HI 2" THEN "Remaining Hawaiian Islands"
ELSEIF [Address Home Eps Code] = "IA 0" THEN "Iowa - Miscellaneous"
ELSEIF [Address Home Eps Code] = "IA 1" THEN "Cedar Rapids & Eastern Iowa"
ELSEIF [Address Home Eps Code] = "IA 2" THEN "Des Moines & Western Iowa"
ELSEIF [Address Home Eps Code] = "ID 0" THEN "Idaho - Miscellaneous"
ELSEIF [Address Home Eps Code] = "ID 1" THEN "Boise City"
ELSEIF [Address Home Eps Code] = "ID 2" THEN "Northern Idaho"
ELSEIF [Address Home Eps Code] = "IL 0" THEN "Illinois - Miscellaneous"
ELSEIF [Address Home Eps Code] = "IL 1" THEN "Rockford"
ELSEIF [Address Home Eps Code] = "IL 2" THEN "Quad Cities"
ELSEIF [Address Home Eps Code] = "IL 3" THEN "Peoria"
ELSEIF [Address Home Eps Code] = "IL 4" THEN "Springfield"
ELSEIF [Address Home Eps Code] = "IL 5" THEN "Decatur & Champaign"
ELSEIF [Address Home Eps Code] = "IL 6" THEN "Southern Illinois"
ELSEIF [Address Home Eps Code] = "IL 7" THEN "Chain of Lakes"
ELSEIF [Address Home Eps Code] = "IL 8" THEN "Northwest Suburbs"
ELSEIF [Address Home Eps Code] = "IL 9" THEN "North Shore"
ELSEIF [Address Home Eps Code] = "IL10" THEN "Evanston & Skokie"
ELSEIF [Address Home Eps Code] = "IL11" THEN "City of Chicago"
ELSEIF [Address Home Eps Code] = "IL12" THEN "Western Suburbs"
ELSEIF [Address Home Eps Code] = "IL13" THEN "South & Southwest Suburbs"
ELSEIF [Address Home Eps Code] = "IN 0" THEN "Indiana - Miscellaneous"
ELSEIF [Address Home Eps Code] = "IN 1" THEN "The Region"
ELSEIF [Address Home Eps Code] = "IN 2" THEN "Northwest Indiana"
ELSEIF [Address Home Eps Code] = "IN 3" THEN "South Bend & Elkhart"
ELSEIF [Address Home Eps Code] = "IN 4" THEN "Northeast Indiana"
ELSEIF [Address Home Eps Code] = "IN 5" THEN "West Central Indiana"
ELSEIF [Address Home Eps Code] = "IN 6" THEN "East Central Indiana"
ELSEIF [Address Home Eps Code] = "IN 7" THEN "Greater Indianapolis"
ELSEIF [Address Home Eps Code] = "IN 8" THEN "West Indiana"
ELSEIF [Address Home Eps Code] = "IN 9" THEN "South Central Indiana"
ELSEIF [Address Home Eps Code] = "IN10" THEN "East Indiana"
ELSEIF [Address Home Eps Code] = "IN11" THEN "Southwest Indiana"
ELSEIF [Address Home Eps Code] = "IN12" THEN "Southeast Indiana"
ELSEIF [Address Home Eps Code] = "KS 0" THEN "Kansas - Miscellaneous"
ELSEIF [Address Home Eps Code] = "KS 1" THEN "Kansas City & Topeka"
ELSEIF [Address Home Eps Code] = "KS 2" THEN "Wichita & Western Kansas"
ELSEIF [Address Home Eps Code] = "KY 0" THEN "Kentucky - Miscellaneous"
ELSEIF [Address Home Eps Code] = "KY 1" THEN "Lexington & Fayette"
ELSEIF [Address Home Eps Code] = "KY 2" THEN "Louisville & Western Kentucky"
ELSEIF [Address Home Eps Code] = "LA 0" THEN "Louisiana - Miscellaneous"
ELSEIF [Address Home Eps Code] = "LA 1" THEN "Baton Rouge"
ELSEIF [Address Home Eps Code] = "LA 2" THEN "New Orleans"
ELSEIF [Address Home Eps Code] = "LA 3" THEN "Shreveport"
ELSEIF [Address Home Eps Code] = "MA 0" THEN "Massachusetts - Miscellaneous"
ELSEIF [Address Home Eps Code] = "MA 1" THEN "Berkshire & Franklin Co"
ELSEIF [Address Home Eps Code] = "MA 2" THEN "Springfield & Hampshire Co"
ELSEIF [Address Home Eps Code] = "MA 3" THEN "Fitchburg & North Worcester Co"
ELSEIF [Address Home Eps Code] = "MA 4" THEN "Essex Co"
ELSEIF [Address Home Eps Code] = "MA 5" THEN "Cape Cod & Islands"
ELSEIF [Address Home Eps Code] = "MA 6" THEN "Boston & Cambridge"
ELSEIF [Address Home Eps Code] = "MA 7" THEN "Quincy & Plymouth Co"
ELSEIF [Address Home Eps Code] = "MA 8" THEN "Lowell, Concord, & Wellesley"
ELSEIF [Address Home Eps Code] = "MA 9" THEN "Norfolk & Bristol Co"
ELSEIF [Address Home Eps Code] = "MA10" THEN "Milton, Lexington, & Waltham"
ELSEIF [Address Home Eps Code] = "MA11" THEN "Worcester"
ELSEIF [Address Home Eps Code] = "MD 0" THEN "Maryland - Miscellaneous"
ELSEIF [Address Home Eps Code] = "MD 1" THEN "Western Maryland"
ELSEIF [Address Home Eps Code] = "MD 2" THEN "Montgomery Metropolitan"
ELSEIF [Address Home Eps Code] = "MD 3" THEN "Central Maryland (w/o Baltimore)"
ELSEIF [Address Home Eps Code] = "MD 4" THEN "Eastern Shore"
ELSEIF [Address Home Eps Code] = "MD 5" THEN "Prince Georges Metropolitan"
ELSEIF [Address Home Eps Code] = "MD 6" THEN "Southern Maryland"
ELSEIF [Address Home Eps Code] = "MD 7" THEN "Baltimore (Urban)"
ELSEIF [Address Home Eps Code] = "ME 0" THEN "Maine - Miscellaneous"
ELSEIF [Address Home Eps Code] = "ME 1" THEN "Portland & Southern Maine"
ELSEIF [Address Home Eps Code] = "ME 2" THEN "Augusta & Central Maine"
ELSEIF [Address Home Eps Code] = "ME 3" THEN "Bangor & Northern Maine"
ELSEIF [Address Home Eps Code] = "MI 0" THEN "Michigan - Miscellaneous"
ELSEIF [Address Home Eps Code] = "MI 1" THEN "Wayne Co"
ELSEIF [Address Home Eps Code] = "MI 2" THEN "Detroit's Northern Suburbs"
ELSEIF [Address Home Eps Code] = "MI 3" THEN "Ann Arbor"
ELSEIF [Address Home Eps Code] = "MI 4" THEN "Capital District"
ELSEIF [Address Home Eps Code] = "MI 5" THEN "Kalamazoo and Grand Rapids"
ELSEIF [Address Home Eps Code] = "MI 6" THEN "The Thumb"
ELSEIF [Address Home Eps Code] = "MI 7" THEN "Northern Michigan"
ELSEIF [Address Home Eps Code] = "MN 0" THEN "Minnesota - Miscellaneous"
ELSEIF [Address Home Eps Code] = "MN 1" THEN "Twin Cities"
ELSEIF [Address Home Eps Code] = "MN 2" THEN "Northern Minnesota"
ELSEIF [Address Home Eps Code] = "MO 0" THEN "Missouri - Miscellaneous"
ELSEIF [Address Home Eps Code] = "MO 1" THEN "Kansas City & St. Joseph"
ELSEIF [Address Home Eps Code] = "MO 2" THEN "St. Louis & Eastern Missouri"
ELSEIF [Address Home Eps Code] = "MO 3" THEN "Springfield & Southern Missouri"
ELSEIF [Address Home Eps Code] = "MS 0" THEN "Mississippi - Miscellaneous"
ELSEIF [Address Home Eps Code] = "MS 1" THEN "Jackson"
ELSEIF [Address Home Eps Code] = "MS 2" THEN "Northern Mississippi"
ELSEIF [Address Home Eps Code] = "MT 0" THEN "Montana - Miscellaneous"
ELSEIF [Address Home Eps Code] = "MT 1" THEN "Billings & Eastern Montana"
ELSEIF [Address Home Eps Code] = "MT 2" THEN "Western Montana"
ELSEIF [Address Home Eps Code] = "NC 0" THEN "North Carolina - Miscellaneous"
ELSEIF [Address Home Eps Code] = "NC 1" THEN "Coastal Plains"
ELSEIF [Address Home Eps Code] = "NC 2" THEN "East Central North Carolina"
ELSEIF [Address Home Eps Code] = "NC 3" THEN "Research Triangle"
ELSEIF [Address Home Eps Code] = "NC 4" THEN "Sand Hills"
ELSEIF [Address Home Eps Code] = "NC 5" THEN "North Piedmont"
ELSEIF [Address Home Eps Code] = "NC 6" THEN "South Piedmont"
ELSEIF [Address Home Eps Code] = "NC 7" THEN "Western North Carolina"
ELSEIF [Address Home Eps Code] = "ND 0" THEN "North Dakota - Miscellaneous"
ELSEIF [Address Home Eps Code] = "ND 1" THEN "Fargo & Eastern North Dakota"
ELSEIF [Address Home Eps Code] = "ND 2" THEN "Western North Dakota"
ELSEIF [Address Home Eps Code] = "NE 0" THEN "Nebraska - Miscellaneous"
ELSEIF [Address Home Eps Code] = "NE 1" THEN "Lincoln"
ELSEIF [Address Home Eps Code] = "NE 2" THEN "Omaha"
ELSEIF [Address Home Eps Code] = "NE 3" THEN "Western Nebraska"
ELSEIF [Address Home Eps Code] = "NH 0" THEN "New Hampshire - Miscellaneous"
ELSEIF [Address Home Eps Code] = "NH 1" THEN "Seacoast"
ELSEIF [Address Home Eps Code] = "NH 2" THEN "Merrimack Valley"
ELSEIF [Address Home Eps Code] = "NH 3" THEN "Monadnock & Lake Sunapee"
ELSEIF [Address Home Eps Code] = "NH 4" THEN "Lakes & White Mountains"
ELSEIF [Address Home Eps Code] = "NJ 0" THEN "New Jersey - Miscellaneous"
ELSEIF [Address Home Eps Code] = "NJ 1" THEN "Southern Jersey"
ELSEIF [Address Home Eps Code] = "NJ 2" THEN "Camden & Burlington Co"
ELSEIF [Address Home Eps Code] = "NJ 3" THEN "Jersey Shore & Pinelands"
ELSEIF [Address Home Eps Code] = "NJ 4" THEN "Middlesex Co"
ELSEIF [Address Home Eps Code] = "NJ 5" THEN "Monmouth Co"
ELSEIF [Address Home Eps Code] = "NJ 6" THEN "Somerset & Mercer Co"
ELSEIF [Address Home Eps Code] = "NJ 7" THEN "Union Co"
ELSEIF [Address Home Eps Code] = "NJ 8" THEN "Essex & Southern Passaic Co"
ELSEIF [Address Home Eps Code] = "NJ 9" THEN "Hudson Co"
ELSEIF [Address Home Eps Code] = "NJ10" THEN "Bergen Co"
ELSEIF [Address Home Eps Code] = "NJ11" THEN "Morris & Northern Passaic Co"
ELSEIF [Address Home Eps Code] = "NJ12" THEN "Sussex, Warren, & Hunterdon Co"
ELSEIF [Address Home Eps Code] = "NM 0" THEN "New Mexico - Miscellaneous"
ELSEIF [Address Home Eps Code] = "NM 1" THEN "Albuquerque & Northern New Mexico"
ELSEIF [Address Home Eps Code] = "NM 2" THEN "Southern New Mexico"
ELSEIF [Address Home Eps Code] = "NV 0" THEN "Nevada - Miscellaneous"
ELSEIF [Address Home Eps Code] = "NV 1" THEN "Las Vegas"
ELSEIF [Address Home Eps Code] = "NV 2" THEN "Reno"
ELSEIF [Address Home Eps Code] = "NY 0" THEN "New York - Miscellaneous"
ELSEIF [Address Home Eps Code] = "NY 1" THEN "Southern Tier West"
ELSEIF [Address Home Eps Code] = "NY 2" THEN "Erie Co"
ELSEIF [Address Home Eps Code] = "NY 3" THEN "Genesee Valley & N. Frontier"
ELSEIF [Address Home Eps Code] = "NY 4" THEN "Rochester & Monroe Co"
ELSEIF [Address Home Eps Code] = "NY 5" THEN "Finger Lakes Region"
ELSEIF [Address Home Eps Code] = "NY 6" THEN "Central New York"
ELSEIF [Address Home Eps Code] = "NY 7" THEN "Saint Lawrence Valley"
ELSEIF [Address Home Eps Code] = "NY 8" THEN "Adirondacks"
ELSEIF [Address Home Eps Code] = "NY 9" THEN "Tri Cities"
ELSEIF [Address Home Eps Code] = "NY10" THEN "Central Hudson Valley"
ELSEIF [Address Home Eps Code] = "NY11" THEN "Catskills"
ELSEIF [Address Home Eps Code] = "NY12" THEN "Southern Tier East"
ELSEIF [Address Home Eps Code] = "NY13" THEN "Rockland Co"
ELSEIF [Address Home Eps Code] = "NY14" THEN "Staten Island"
ELSEIF [Address Home Eps Code] = "NY15" THEN "Westchester Co"
ELSEIF [Address Home Eps Code] = "NY16" THEN "Southern Nassau Co"
ELSEIF [Address Home Eps Code] = "NY17" THEN "Northern Nassau Co"
ELSEIF [Address Home Eps Code] = "NY18" THEN "Central Nassau Co"
ELSEIF [Address Home Eps Code] = "NY19" THEN "Northwest Suffolk Co"
ELSEIF [Address Home Eps Code] = "NY20" THEN "Southwest Suffolk Co"
ELSEIF [Address Home Eps Code] = "NY21" THEN "East Suffolk Co"
ELSEIF [Address Home Eps Code] = "NY22" THEN "Southeast Brooklyn"
ELSEIF [Address Home Eps Code] = "NY23" THEN "West Brooklyn"
ELSEIF [Address Home Eps Code] = "NY24" THEN "Northeast Brooklyn"
ELSEIF [Address Home Eps Code] = "NY25" THEN "East Bronx"
ELSEIF [Address Home Eps Code] = "NY26" THEN "West Bronx"
ELSEIF [Address Home Eps Code] = "NY27" THEN "Manhattan"
ELSEIF [Address Home Eps Code] = "NY28" THEN "South Queens"
ELSEIF [Address Home Eps Code] = "NY29" THEN "Northwest Queens"
ELSEIF [Address Home Eps Code] = "NY30" THEN "Northeast Queens"
ELSEIF [Address Home Eps Code] = "OH 0" THEN "Ohio - Miscellaneous"
ELSEIF [Address Home Eps Code] = "OH 1" THEN "Northwest Ohio"
ELSEIF [Address Home Eps Code] = "OH 2" THEN "North Central Ohio"
ELSEIF [Address Home Eps Code] = "OH 3" THEN "City of Cleveland (West)"
ELSEIF [Address Home Eps Code] = "OH 4" THEN "City of Cleveland (East)"
ELSEIF [Address Home Eps Code] = "OH 5" THEN "Cuyahoga, Geauga, & Lake Co"
ELSEIF [Address Home Eps Code] = "OH 6" THEN "Northeast Ohio"
ELSEIF [Address Home Eps Code] = "OH 7" THEN "West Central Ohio"
ELSEIF [Address Home Eps Code] = "OH 8" THEN "Central Ohio"
ELSEIF [Address Home Eps Code] = "OH 9" THEN "Greater Cincinnati"
ELSEIF [Address Home Eps Code] = "OH10" THEN "Southeast Ohio"
ELSEIF [Address Home Eps Code] = "OK 0" THEN "Oklahoma - Miscellaneous"
ELSEIF [Address Home Eps Code] = "OK 1" THEN "Oklahoma City & Western Oklahoma"
ELSEIF [Address Home Eps Code] = "OK 2" THEN "Tulsa & Eastern Oklahoma"
ELSEIF [Address Home Eps Code] = "OR 0" THEN "Oregon - Miscellaneous"
ELSEIF [Address Home Eps Code] = "OR 1" THEN "Greater Portland (West)"
ELSEIF [Address Home Eps Code] = "OR 2" THEN "Greater Portland (East)"
ELSEIF [Address Home Eps Code] = "OR 3" THEN "Northern Valley (Coast)"
ELSEIF [Address Home Eps Code] = "OR 4" THEN "Southern Valley"
ELSEIF [Address Home Eps Code] = "OR 5" THEN "Southwest Oregon"
ELSEIF [Address Home Eps Code] = "OR 6" THEN "East Oregon"
ELSEIF [Address Home Eps Code] = "PA 0" THEN "Pennsylvania - Miscellaneous"
ELSEIF [Address Home Eps Code] = "PA 1" THEN "Bucks Co"
ELSEIF [Address Home Eps Code] = "PA 2" THEN "Chester Co"
ELSEIF [Address Home Eps Code] = "PA 3" THEN "Delaware Co"
ELSEIF [Address Home Eps Code] = "PA 4" THEN "Montgomery Co"
ELSEIF [Address Home Eps Code] = "PA 5" THEN "Philadelphia Co"
ELSEIF [Address Home Eps Code] = "PA 6" THEN "Lehigh Valley"
ELSEIF [Address Home Eps Code] = "PA 7" THEN "Northeastern Pennsylvania"
ELSEIF [Address Home Eps Code] = "PA 8" THEN "North Central Pennsylvania"
ELSEIF [Address Home Eps Code] = "PA 9" THEN "Northwestern Pennsylvania"
ELSEIF [Address Home Eps Code] = "PA10" THEN "Southern Pennsylvania (East)"
ELSEIF [Address Home Eps Code] = "PA11" THEN "Southern Pennsylvania (West)"
ELSEIF [Address Home Eps Code] = "PA12" THEN "Allegheny Co"
ELSEIF [Address Home Eps Code] = "PA13" THEN "Southwestern PA (w/o Allegheny Co)"
ELSEIF [Address Home Eps Code] = "RI 0" THEN "Rhode Island - Miscellaneous"
ELSEIF [Address Home Eps Code] = "RI 1" THEN "Providence & N. Rhode Island"
ELSEIF [Address Home Eps Code] = "RI 2" THEN "Southern Rhode Island"
ELSEIF [Address Home Eps Code] = "SC 0" THEN "South Carolina - Miscellaneous"
ELSEIF [Address Home Eps Code] = "SC 1" THEN "Pee Dee"
ELSEIF [Address Home Eps Code] = "SC 2" THEN "Low Country"
ELSEIF [Address Home Eps Code] = "SC 3" THEN "Mid Lands"
ELSEIF [Address Home Eps Code] = "SC 4" THEN "East Piedmont"
ELSEIF [Address Home Eps Code] = "SC 5" THEN "West Piedmont"
ELSEIF [Address Home Eps Code] = "SD 0" THEN "South Dakota - Miscellaneous"
ELSEIF [Address Home Eps Code] = "SD 1" THEN "Sioux Falls & Eastern South Dakota"
ELSEIF [Address Home Eps Code] = "SD 2" THEN "Western South Dakota"
ELSEIF [Address Home Eps Code] = "TN 0" THEN "Tennessee - Miscellaneous"
ELSEIF [Address Home Eps Code] = "TN 1" THEN "Chattanooga"
ELSEIF [Address Home Eps Code] = "TN 2" THEN "Knoxville"
ELSEIF [Address Home Eps Code] = "TN 3" THEN "Memphis"
ELSEIF [Address Home Eps Code] = "TN 4" THEN "Nashville & Davidson"
ELSEIF [Address Home Eps Code] = "TX 0" THEN "Texas - Miscellaneous"
ELSEIF [Address Home Eps Code] = "TX 1" THEN "Amarillo, Panhandle, & South Plains"
ELSEIF [Address Home Eps Code] = "TX 2" THEN "El Paso"
ELSEIF [Address Home Eps Code] = "TX 3" THEN "Midland, Odessa, & Trans Pecos"
ELSEIF [Address Home Eps Code] = "TX 4" THEN "Abilene & San Angelo"
ELSEIF [Address Home Eps Code] = "TX 5" THEN "Red River Area"
ELSEIF [Address Home Eps Code] = "TX 6" THEN "Austin & Central Texas"
ELSEIF [Address Home Eps Code] = "TX 7" THEN "Waco, Temple, & Killeen"
ELSEIF [Address Home Eps Code] = "TX 8" THEN "East Texas"
ELSEIF [Address Home Eps Code] = "TX 9" THEN "Beaumont & Port Arthur"
ELSEIF [Address Home Eps Code] = "TX10" THEN "Gulf Coast, Wharton, & Victoria Co"
ELSEIF [Address Home Eps Code] = "TX11" THEN "South Texas Valley"
ELSEIF [Address Home Eps Code] = "TX12" THEN "Brazos & Trinity Valley"
ELSEIF [Address Home Eps Code] = "TX13" THEN "Del Rio, Uvalde Co, & Bexar Co"
ELSEIF [Address Home Eps Code] = "TX14" THEN "City of San Antonio"
ELSEIF [Address Home Eps Code] = "TX15" THEN "Northwest Houston & Conroe School Dist"
ELSEIF [Address Home Eps Code] = "TX16" THEN "Southwest Houston Metro Area"
ELSEIF [Address Home Eps Code] = "TX17" THEN "City of Houston (East)"
ELSEIF [Address Home Eps Code] = "TX18" THEN "Galveston & East Harris Co"
ELSEIF [Address Home Eps Code] = "TX19" THEN "City of Dallas"
ELSEIF [Address Home Eps Code] = "TX20" THEN "City of Fort Worth"
ELSEIF [Address Home Eps Code] = "TX21" THEN "Irving, Arlington, & Grand Prairie"
ELSEIF [Address Home Eps Code] = "TX22" THEN "Dallas Co (w/o City of Dallas)"
ELSEIF [Address Home Eps Code] = "TX23" THEN "Collin & Rockwall Co"
ELSEIF [Address Home Eps Code] = "TX24" THEN "West of Dallas/Ft. Worth Metroplex"
ELSEIF [Address Home Eps Code] = "UT 0" THEN "Utah - Miscellaneous"
ELSEIF [Address Home Eps Code] = "UT 1" THEN "Salt Lake City, Ogden, & Provo"
ELSEIF [Address Home Eps Code] = "UT 2" THEN "Southern Utah"
ELSEIF [Address Home Eps Code] = "VA 0" THEN "Virginia - Miscellaneous"
ELSEIF [Address Home Eps Code] = "VA 1" THEN "Arlington & Alexandria"
ELSEIF [Address Home Eps Code] = "VA 2" THEN "Fairfax Co"
ELSEIF [Address Home Eps Code] = "VA 3" THEN "North Central Virginia"
ELSEIF [Address Home Eps Code] = "VA 4" THEN "Northern Neck"
ELSEIF [Address Home Eps Code] = "VA 5" THEN "Central Virginia"
ELSEIF [Address Home Eps Code] = "VA 6" THEN "Richmond"
ELSEIF [Address Home Eps Code] = "VA 7" THEN "Southside Virginia"
ELSEIF [Address Home Eps Code] = "VA 8" THEN "Tidewater"
ELSEIF [Address Home Eps Code] = "VA 9" THEN "Shenandoah"
ELSEIF [Address Home Eps Code] = "VA10" THEN "Southwest Virginia"
ELSEIF [Address Home Eps Code] = "VT 1" THEN "Burlington"
ELSEIF [Address Home Eps Code] = "VT 2" THEN "Southern Vermont"
ELSEIF [Address Home Eps Code] = "VT 3" THEN "Northern & Eastern Vermont"
ELSEIF [Address Home Eps Code] = "WA 0" THEN "Washington - Miscellaneous"
ELSEIF [Address Home Eps Code] = "WA 1" THEN "Greater Seattle"
ELSEIF [Address Home Eps Code] = "WA 2" THEN "South Sound"
ELSEIF [Address Home Eps Code] = "WA 3" THEN "Greater Spokane"
ELSEIF [Address Home Eps Code] = "WA 4" THEN "Greater Washington (East)"
ELSEIF [Address Home Eps Code] = "WA 5" THEN "Greater Washington (West)"
ELSEIF [Address Home Eps Code] = "WA 6" THEN "Bellingham Area"
ELSEIF [Address Home Eps Code] = "WI 0" THEN "Wisconsin - Miscellaneous"
ELSEIF [Address Home Eps Code] = "WI 1" THEN "Madison and Janesville"
ELSEIF [Address Home Eps Code] = "WI 2" THEN "Milwaukee and Racine"
ELSEIF [Address Home Eps Code] = "WI 3" THEN "Northern Wisconsin"
ELSEIF [Address Home Eps Code] = "WV 0" THEN "West Virginia - Miscellaneous"
ELSEIF [Address Home Eps Code] = "WV 1" THEN "Charleston & Huntington"
ELSEIF [Address Home Eps Code] = "WV 2" THEN "Northern West Virginia"
ELSEIF [Address Home Eps Code] = "WY 0" THEN "Wyoming - Miscellaneous"
ELSEIF [Address Home Eps Code] = "WY 1" THEN "Casper & Cheyenne"
ELSEIF [Address Home Eps Code] = "WY 2" THEN "Western Wyoming"
END
```
