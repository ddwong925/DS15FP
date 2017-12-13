@title[Introduction]
## My first Data Science Project
<span style="font-family:Helvetica Neue; font-weight:bold">From <span style="color:#e49436">NOTHING</span> to <span style="color:#e49436">SOMETHNG</span></span>

---
@title[My first DS project]
## Scope:

##### @fa[thumbs-up] Think BIG and ...

+++
@title[scope]
## Create a better model than the Hong Kong Observatory.

+++
##### <span style="color:#e49436">TO-DO:</span>
- Collect and scrape data
- Clean them 
- Parse data 
- Mine the data 
- Build the model 
- Test the model 
- <span style="color:red;font-weight:bold">Presenation </span>

---
##### <span style="color:#e49436">TO-DO:</span>
- <span style="color:red;font-weight:bold">Collect and scrape data </span>
- Clean them 
- Parse data 
- Mine the data 
- Build the model 
- Test the model 
- Presenation 

+++
@title[bs4]
## I heard
<span style="font-size:1.3em">"beautifulsoup is easy to understand and build"</span>
<br>
- Cool! I will build it with beautifulsoup then |


+++?image=assets/WU.png&size=contain
+++?image=assets/WU2.png&size=contain

+++
@title[bs1.1]
##### <span style="color:#e49436">My 1st crawler:</span> Copy and paste (MOSTLY)

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
@title[bs1.3]
<span style="font-size:1.2em ; color:#e49436">After N-th trial, I got my first set of data</span>
<br>
- 300,000 rows+
- 10+ features |

+++
@title[bs1.2]
##### oops, something's missing
- Error handling
- Data inconsistency

+++?image=assets/WU4.png&size=contain
+++?image=assets/WU3.png&size=contain
+++?image=assets/WU5.png&size=contain
+++
@title[bs1.4]
<strike> My first set of data </strike>
- Time to build my 2nd Crawler... |

+++?image=assets/HKO1.png&size=contain
+++
@title[bs2.1]
##### <span style="color:#e49436"> My 2nd crawler:</span> Copy and paste (AGAIN)
```python
try:
        text = str(soup.find('pre').text)

        timestamp = str(y)+'-'+str(mm)+'-'+str(dd)
        print timestamp

        max_temp_text = re.search('MAX.*[0123456789.]+',text, re.IGNORECASE).group(0)
        max_temp = re.findall('[0123456789.]+',max_temp_text)
        print max_temp
        min_temp_text = re.search('MIN.*[0123456789.]+',text, re.IGNORECASE).group(0)
        min_temp = re.findall('[0123456789.]+',min_temp_text)
        print min_temp
        grass_temp_text = re.search('GRASS.*[0123456789.]+',text, re.IGNORECASE).group(0)
        grass_temp = re.findall('[0123456789.]+',grass_temp_text)
        print grass_temp
        min_hum_text = re.search('REL.*[0123456789.]+.*-',text, re.IGNORECASE).group(0)
        min_hum = re.findall('[0123456789.]+',min_hum_text)
        print min_hum
        max_hum_text = re.search('-.*[0123456789.]+.*PER',text, re.IGNORECASE).group(0)
        max_hum = re.findall('[0123456789.]+',max_hum_text)
        print max_hum
        rainfall_text = re.search('RAIN.*\n',text, re.IGNORECASE).group(0)
        rainfall = re.findall('[0123456789.]+|TRACE|trace|Trace',rainfall_text)
        print rainfall
        sun_text = re.search('DURA.*[0123456789.]+',text, re.IGNORECASE).group(0)
        sun = re.findall('[0123456789.]+',sun_text)
        print sun     
        uv_text = re.search('UV INDEX.*\n.*INTEN', text, re.IGNORECASE).group(0)
        uv = re.findall('[0123456789.]+',uv_text)
        print uv
      
      except:
        text = np.nan
```
@[1-30](Time to learn Regex!)

+++
@title[bs2.2]
### <span style="color:#e49436">Results from 2nd Crawler</span>
- 6460 rows of data
- 8 features |
- HKO data is cleaner |

---
@title[Parse1]
##### <span style="color:#e49436">TO-DO:</span>
- <strike>Collect and scrape data </strike>
- <strike>Clean them </strike>
- <span style="color:red;font-weight:bold">Parse data </span>
- Mine the data 
- Build the model 
- Test the model 
- Presenation 

+++
@title[Parse2]
##### <span style="color:#e49436">Parsing the data</span>
```python
train = pd.read_csv("HK_weather.csv",index_col=None,parse_dates = [1])

train['Date'] = pd.to_datetime(train['Date'])
train.set_index('Date', inplace = True)

train.sample(10)
```
+++?image=assets/P1.png&size=contain
+++
@title[data_dict]
##### <span style="color:#e49436"> Data Dictionary</span>

1. __Date__: 2000-4-1 to 2017-12-8
> - Training set: 2000-4-1 to 2012-8-17
> - Testing set: 2012-8-18 to 2017-12-7
2. __max_temp__: Maximum temperature of the day (Degree Celcius)
3. __min_temp__: Minimum temperature of the day (Degree Celcius)
4. ... ...

+++?image=assets/P2.png&size=contain


---
@title[mine1]
##### <span style="color:#e49436">TO-DO:</span>
- <strike>Collect and scrape data </strike>
- <strike>Clean them </strike>
- <strike>Parse data </strike>
- <span style="color:red;font-weight:bold">Mine the data</span>
- Build the model 
- Test the model 
- Presenation

+++?image=assets/P3.png&size=contain
+++?image=assets/acf.png&size=contain
+++?image=assets/P4.png&size=contain

---
@title[build]
##### <span style="color:#e49436">TO-DO:</span>
- <strike>Collect and scrape data </strike>
- <strike>Clean them </strike>
- <strike>Parse data </strike>
- <strike>Mine the data</strike>
- <span style="color:red;font-weight:bold">Build the model </span>
- Test the model 
- Presenation

+++
@title[arima100]
##### Build the model with AR(1)
```python
model = ARIMA(train['max_temp'],(1, 0, 0)).fit()

predictions = model.predict(
    '2012-8-18',
    '2017-12-7',
    dynamic=False, 
)

print("Mean absolute error: ", mean_absolute_error(test['max_temp'], predictions))
model.summary()
```
@[1-10](Mean absolute error = 4.723896)
+++?image=assets/m1.png&size=contain
+++
@title[arima101]
##### Build the model with ARMA(1,1)
```python
model = ARIMA(train['max_temp'], (1,0,1)).fit()

predictions = model.predict(
    '2012-8-18',
    '2017-12-7',
    dynamic=True,
    )
print("Mean absolute error: ", mean_absolute_error(test['max_temp'], predictions))
model.summary()

# From the result, we can see the ARMA(2,2) helps lowering my mean absolute error
```
@[1-11](Mean absolute error = 4.723468)
+++?image=assets/m2.png&size=contain

+++
@title[arima111]
##### Build the model with ARIMA(1,1,1)
```python
model = ARIMA(train['max_temp'], (1,1,1)).fit()

predictions = model.predict(
    '2012-8-18',
    '2017-12-7',
    dynamic=True,
    )
print("Mean absolute error: ", mean_absolute_error(test['max_temp'], predictions))
model.summary()
```
@[1-9](Mean absolute error =  26.316174)
+++?image=assets/m3.png&size=contain
+++?image=assets/d2.png&size=contain
+++
@title[GS]
##### Finding the best model
```python
for p in range(1,3):
    for q in range (0,3):
        for d in [0,1]:
            model = ARIMA(train['max_temp'], (p,d,q)).fit()

            predictions = model.predict(
                '2012-8-18',
                '2017-12-7',
                dynamic=True,
                )
            print("p:{}, q:{},d:{}, Mean absolute error: ".format(p,q,d), mean_absolute_error(test['max_temp'], predictions))
```
+++?image=assets/GS.png&size=contain
---
@title[test]
##### <span style="color:#e49436">TO-DO:</span>
- <strike>Collect and scrape data </strike>
- <strike>Clean them </strike>
- <strike>Parse data </strike>
- <strike>Mine the data</strike>
- <strike>Build the model </strike>
- <span style="color:red;font-weight:bold">Test the model </span>
- Presenation
+++?image=assets/pred0.png&size=contain
+++?image=assets/pred1.png&size=contain

+++
@title[pred]
##### PRINT Final result.head()

| Date | Actual data | Prediction |
| ---- | :------: | :--------: |
| 2012-08-18 | 32.6 | 30.05016 |
| 2012-08-19 | 32.0 | 30.69538 |
| 2012-08-20 | 32.9 | 30.61853 |
| 2012-08-21 | 31.6 | 30.54294 |
| 2012-08-22 | 28.9 | 30.46858 |

---
@title[pre]
##### <span style="color:#e49436">TO-DO:</span>
- <strike>Collect and scrape data </strike>
- <strike>Clean them </strike>
- <strike>Parse data </strike>
- <strike>Mine the data</strike>
- <strike>Build the model </strike>
- <strike>Test the model </strike>
- <span style="color:red;font-weight:bold"> Presenation </span>

+++
@title[next]
##### Next
- Get more data?
- SARIMAX model: include seasonality |
- Features in ARIMA : input extra features in the ARIMA |
- MORE STUDY and GOOD LUCK TO ALL |
- Q & A? |
