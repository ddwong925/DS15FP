@title[Introduction]
## My first Data Science Project
<span style="font-family:Helvetica Neue; font-weight:bold">From <span style="color:#e49436">nothing</span> to <span style="color:#e49436"> something</span></span>

---
@title[My first DS project]
## Scope:

##### @fa[thumbs-up] Think BIG and ...

+++
@title[scope]
## Create a better model than the Hong Kong Observatory.

+++
##### Like most of us, TO-DO:
- Collect and scrape data
- Clean them |
- Parse data |
- Mine the data |
- Refine |
- Build the model |
- <span style="color:red;font-weight:bold">Presenation </span> |

---
@title[bs4]
# I heard
<span style="font-size:1.2em">"beautifulsoup is easy to understand and build"</span>
<br>
- Cool! I will build it with beautifulsoup then :D |


+++?image=assets/WU.png&size=contain
+++?image=assets/WU2.png&size=contain

+++
@title[scrape]
##### TO-DO:
- <span style="color:red;font-weight:bold">Collect and scrape data </span>
- Clean them 
- Parse data 
- Mine the data 
- Refine 
- Build the model 
- Presenation 

+++
@title[bs1.1]
### My 1st crawler:<span class="gold"> Copy and paste (MOSTLY)</span>

```python
f = open('wunder-data.txt', 'w')

# set cookie as showmetar = 1
r = requests.Session()
metar = r.get('http://www.wunderground.com/cgi-bin/findweather/getForecast?setpref=SHOWMETAR&value=1')

# Iterate through year, month, and day
for y in range(2000, 2001):
  for m in range(5, 13):
    for d in range(1, 32):
 
      # Check if leap year
      if y%400 == 0:
        leap = True
      elif y%100 == 0:
        leap = False
      elif y%4 == 0:
        leap = True
      else:
        leap = False
 
      # Check if already gone through month
      if (m == 2 and leap and d > 29):
        continue
      elif (m == 2 and d > 28):
        continue
      elif (m in [4, 6, 9, 11] and d > 30):
        continue
 

      # Open wunderground.com url
      url = "http://www.wunderground.com/history/airport/VHHH/"+str(y)+ "/" + str(m) + "/" + str(d) + "/DailyHistory.html"
      page = r.get(url)
      
 
      # Get temperature from page
      soup = BeautifulSoup(page.text, "lxml")

      HistoryTable = soup.find('table', id='historyTable')
# spans = HistoryTable.find_all(attrs={'class':'no-metars'})

      col = []
      for row in HistoryTable.findAll("th"):
        col.append(row.text.strip())

      for row in HistoryTable.findAll("tr")[1::2]:
        col = row.findAll("td")
      
        Time = col[0].string.strip()
        Temp = col[1].text.strip()
        Dew_point = col[2].text.strip()
        Humidity = col[3].text.strip()
        Pressure = col[4].text.strip()
        Visibility = col[5].text.strip()
        Wind_Dir = col[6].text.strip()
        Wind_Speed = col[7].text.strip()
        Gust_Speed = col[8].text.strip()
        Precip = col[9].text.strip()
        Events = col[10].text.strip()
        Conditions = col[11].text.strip()

      for row in HistoryTable.findAll("tr")[2::2]:
        col = row.findAll("td")
        Metar = col[1].text.strip()
 
      # Format month for timestamp
        if len(str(m)) < 2:
          mStamp = '0' + str(m)
        else:
          mStamp = str(m)
   
        # Format day for timestamp
        if len(str(d)) < 2:
          dStamp = '0' + str(d)
        else:
          dStamp = str(d)
   
        # Build timestamp
        timestamp = str(y) + mStamp + dStamp
        print timestamp
	  
	  # Write timestamp and temperature to file
        f.write((timestamp + "," +
          Time + "," +
          Temp + "," +
          Dew_point + "," +
          Humidity + "," +
          Pressure + "," +
          Visibility + "," +
          Wind_Dir + "," +
          Gust_Speed + "," +
          Precip + "," +
          Events + "," +
          Conditions + "," +
          Metar + "," +
          "\n").encode("utf-8-sig"))
f.close()
```
@[46-60](Table extraction)
@[83-96](Export to .CSV)
@[13-28](Lifesaver: Leap Year Handling)

+++
@title[bs1.2]
### oops, something's missing
- Error handling
- Data inconsistency

+++?image=assets/WU3.png&size=contain

+++
@title[bs1.3]
<span style="font-size:1.2em">After N-th trial, I got my first set of data</span>
<br>
+++?image=assets/WU4.png&size=contain
+++?image=assets/WU5.png&size=contain
+++
@title[bs1.4]
<strike> My first set of data </strike>
- Time to build my 2nd Crawler... |

+++?image=assets/HKO1.png&size=contain

