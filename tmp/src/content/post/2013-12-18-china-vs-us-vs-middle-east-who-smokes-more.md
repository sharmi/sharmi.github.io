---
 
title: China Vs US Vs Middle East - Who smokes more?
pubDate: 2013-12-18 12:59:04.000000000 +05:30

category:  Uncategorised
tags: []
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2918202632'
  _yoast_wpseo_primary_category: ''
author:  sharmi
 
canonical: "https://www.minvolai.com/china-vs-us-vs-middle-east-who-smokes-more/"
slug: china-vs-us-vs-middle-east-who-smokes-more
---
<p>Nothing helps like practice. I'm fiddling around with machine learning and data visualization libraries to become proficient in them. So I wanted to try my hand in coaxing information from data. Thats when I came across CO<sub>2</sub> emission index of countries in Quandl, one of the excellent sources of open data. So I thought how would it be to take other attributes of the countries and find a relation between those attributes and CO<sub>2</sub> emission index.</p>
<p>Below is the code for scraping the data from Quandl. We are scraping the data because the data we are interested is from the summary page and not individual data source per se. At this point, I'm obligated to mention that quandl provides ability to download data as csv for any timeframe range and your choice of frequency. The same can also be obtained through their excellent python library.</p>
<pre><code class="language-python">import sys
import os
from lxml import html
import requests
import csv

def main(url, outfile):
dirname = os.path.dirname(outfile)
if dirname and not os.path.exists(dirname):
os.makedirs(dirname)
source = requests.get(url)
if not source.status_code == 200:
print "Got status code", source.status_code, "Exiting"
sys.exit(1)

doc = html.fromstring(source.text)
records = [('country', 'value', 'unit', 'year')]
table = doc.xpath('//div[@class="datatable"]')[-1] #The last table contains World data
rows = table.xpath(".//tr")
print len(rows)
for row in rows:
try:
tds = row.xpath('td')
if not tds: continue
country = str((tds[0].xpath('a/@data-sort-value') +
tds[0].xpath('span/@data-sort-value')
)[0])
value = str(tds[1].xpath('span/@data-sort-value')[0])
if value:
value = float(value)
unit = tds[2].text
year = str(tds[3].xpath('span/@data-sort-value')[0])
if year:
year = int(year)
except:
import traceback
print traceback.format_exception(*sys.exc_info())
print html.tostring(row)
else:
records.append((country, value, unit, year))
csv.writer(open(outfile, 'w')).writerows(records)

if __name__ == "__main__":
main(sys.argv[1], sys.argv[2])

</code></pre>
<p>The above code can be used to download tabular data for 'All Countries' from quandl. The data we are interested in are<br />
* <a href="http://www.quandl.com/economics/gdp-all-countries">GDP - All Countries</a><br />
* <a href="http://www.quandl.com/energy/fuelwood-production-all-countries">Fuelwood Production - All Countries</a><br />
* <a href="http://www.quandl.com/demography/urban-population-all-countries">Urban Population - All Countries</a><br />
* <a href="http://www.quandl.com/demography/gini-index-all-countries">Gini Index - All Countries</a><br />
* <a href="http://www.quandl.com/society/passenger-vehicles-all-countries">Passenger Vehicle - All Countries</a><br />
* <a href="http://www.quandl.com/society/cell-phone-subscriptions-all-countries">Cellphone Subscription - All Countries</a><br />
* <a href="http://www.quandl.com/economics/gdp-per-capita-at-ppp-all-countries">GDP -PPP - All Countries</a><br />
* <a href="http://www.quandl.com/economics/population-all-countries">Population - All Countries</a></p>
<p>And there is the main data that we are going to work on : <a href="http://www.quandl.com/browse/worldbank/world-development-indicators/climate-change/environment/co2-emissions-kt-all-countries">"CO<sub>2</sub> Emissions - All Countries"</a></p>
<p>We are going to find which of the above data best predicts the CO<sub>2</sub> emission a country would have.</p>
<pre><code class="language-python">import matplotlib as plot
import numpy as np
import pandas as pd
from mpltools import style

style.use('ggplot')

</code></pre>
<pre><code class="language-python">from pprint import pprint
from glob import glob
csvs = glob('*.csv')
pprint(csvs)

</code></pre>
<pre><code class="language-python">['cellphone.csv',
'urbanpop.csv',
'population.csv',
'gdp.csv',
'gdp_ppp.csv',
'fuelwood.csv',
'co2_emissions.csv',
'vehicles.csv',
'gini.csv']

</code></pre>
<p>First lets read in the CO<sub>2</sub> emissions for each country and calculate CO<sub>2</sub> emission per 1000 people. Then let us bring the rest of the params to the same denomination of 1000 people where possible.</p>
<pre><code class="language-python">co2_em = pd.read_csv('co2_emissions.csv', index_col=0)
co2_em[:5]
population = pd.read_csv('population.csv', index_col=0)
population[:5]
data = pd.DataFrame(co2_em['value'])
data['pop'] = population['value'] * 1000 #Population is in millions. We are converting it to 1000s
data['co2_1000'] = data['value']/data['pop']
data[:5]

</code></pre>
<div style="max-height: 1000px; max-width: 1500px; overflow: auto;">
<table class="dataframe" border="1">
<thead>
<tr style="text-align: right;">
<th></th>
<th>value</th>
<th>pop</th>
<th>co2_1000</th>
</tr>
<tr>
<th>country</th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr>
<th>afghanistan</th>
<td>6314.574</td>
<td>32017</td>
<td>0.197226</td>
</tr>
<tr>
<th>albania</th>
<td>3006.940</td>
<td>3243</td>
<td>0.927209</td>
</tr>
<tr>
<th>algeria</th>
<td>121311.694</td>
<td>36494</td>
<td>3.324154</td>
</tr>
<tr>
<th>angola</th>
<td>26655.423</td>
<td>20213</td>
<td>1.318727</td>
</tr>
<tr>
<th>antigua and barbuda</th>
<td>462.042</td>
<td>88</td>
<td>5.250477</td>
</tr>
</tbody>
</table>
</div>
<pre><code class="language-python">cellphone_usage = pd.read_csv('cellphone.csv', index_col=0) #cellphone connections per 100 persons
data['cellphone'] = cellphone_usage['value'] * 10 #converting it to per 1000 persons
urbanpop = pd.read_csv('urbanpop.csv', index_col=0) #urban pop in percentage
data['urbanpop'] = urbanpop['value']
gdp = pd.read_csv('gdp.csv', index_col=0) #gdp in billions
data['gdp'] = gdp['value'] * 1000000000/(data['pop']*1000) #gdp per person
fuelwood = pd.read_csv('fuelwood.csv', index_col=0)#kiloTonnes
data['fuelwood'] = fuelwood['value']* 1000/data['pop'] #fuelwood in tonnes per person
gini = pd.read_csv('gini.csv', index_col=0)
data['gini'] = gini['value']
vehicles = pd.read_csv('vehicles.csv', index_col=0) # vehicles per thousand people
data['vehicles'] = vehicles['value']
gdp_ppp = pd.read_csv('gdp_ppp.csv', index_col=0)
data['gdp_ppp'] = gdp_ppp['value']
data[:10]

</code></pre>
<div style="max-height: 1000px; max-width: 1500px; overflow: auto;">
<table class="dataframe" border="1">
<thead>
<tr style="text-align: right;">
<th></th>
<th>value</th>
<th>pop</th>
<th>co2_1000</th>
<th>cellphone</th>
<th>urbanpop</th>
<th>gdp</th>
<th>fuelwood</th>
<th>gini</th>
<th>vehicles</th>
<th>gdp_ppp</th>
</tr>
<tr>
<th>country</th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr>
<th>afghanistan</th>
<td>6314.574</td>
<td>32017</td>
<td>0.197226</td>
<td>538.969630</td>
<td>23.8554</td>
<td>621.732205</td>
<td>49.896899</td>
<td>27.82</td>
<td>20.112597</td>
<td>1053.813</td>
</tr>
<tr>
<th>albania</th>
<td>3006.940</td>
<td>3243</td>
<td>0.927209</td>
<td>1084.473347</td>
<td>54.4472</td>
<td>3912.426765</td>
<td>301.031517</td>
<td>34.51</td>
<td>91.979675</td>
<td>8052.178</td>
</tr>
<tr>
<th>algeria</th>
<td>121311.694</td>
<td>36494</td>
<td>3.324154</td>
<td>1033.058644</td>
<td>73.7060</td>
<td>5693.922289</td>
<td>7.184152</td>
<td>35.33</td>
<td>75.872877</td>
<td>7477.069</td>
</tr>
<tr>
<th>angola</th>
<td>26655.423</td>
<td>20213</td>
<td>1.318727</td>
<td>486.100024</td>
<td>59.9062</td>
<td>5873.398308</td>
<td>982.806239</td>
<td>42.66</td>
<td>8.000000</td>
<td>6346.742</td>
</tr>
<tr>
<th>antigua and barbuda</th>
<td>462.042</td>
<td>88</td>
<td>5.250477</td>
<td>1986.178323</td>
<td>29.8672</td>
<td>13363.636364</td>
<td>NaN</td>
<td>NaN</td>
<td>153.276256</td>
<td>18026.662</td>
</tr>
<tr>
<th>argentina</th>
<td>174717.882</td>
<td>41028</td>
<td>4.258504</td>
<td>1425.117584</td>
<td>92.6406</td>
<td>11576.338111</td>
<td>113.385980</td>
<td>44.49</td>
<td>NaN</td>
<td>18112.330</td>
</tr>
<tr>
<th>armenia</th>
<td>4492.075</td>
<td>3366</td>
<td>1.334544</td>
<td>1068.789619</td>
<td>64.1636</td>
<td>2990.493167</td>
<td>11.883541</td>
<td>31.30</td>
<td>94.000000</td>
<td>5838.258</td>
</tr>
<tr>
<th>australia</th>
<td>400194.378</td>
<td>22766</td>
<td>17.578599</td>
<td>1061.928152</td>
<td>89.3372</td>
<td>67723.666872</td>
<td>471.270955</td>
<td>35.19</td>
<td>556.185481</td>
<td>42640.275</td>
</tr>
<tr>
<th>austria</th>
<td>62313.331</td>
<td>8466</td>
<td>7.360422</td>
<td>1612.069881</td>
<td>67.8808</td>
<td>47081.738720</td>
<td>733.052303</td>
<td>29.15</td>
<td>529.338286</td>
<td>42408.575</td>
</tr>
<tr>
<th>azerbaijan</th>
<td>49075.461</td>
<td>9235</td>
<td>5.314073</td>
<td>1074.721324</td>
<td>53.8886</td>
<td>7450.351922</td>
<td>32.658365</td>
<td>33.71</td>
<td>83.849698</td>
<td>10478.231</td>
</tr>
</tbody>
</table>
</div>
<p>Panda's Dataframe provides a convenient function 'corr' that provides the<br />
correlation between each and every column of the dataframe.</p>
<pre><code class="language-python">data.corr()

</code></pre>
<div style="max-height: 1000px; max-width: 1500px; overflow: auto;">
<table class="dataframe" border="1">
<thead>
<tr style="text-align: right;">
<th></th>
<th>value</th>
<th>pop</th>
<th>co2_1000</th>
<th>cellphone</th>
<th>urbanpop</th>
<th>gdp</th>
<th>fuelwood</th>
<th>gini</th>
<th>vehicles</th>
<th>gdp_ppp</th>
</tr>
</thead>
<tbody>
<tr>
<th>value</th>
<td>1.000000</td>
<td>0.795840</td>
<td>0.156284</td>
<td>0.017960</td>
<td>0.100557</td>
<td>0.101115</td>
<td>-0.096348</td>
<td>-0.032174</td>
<td>0.070360</td>
<td>0.116638</td>
</tr>
<tr>
<th>pop</th>
<td>0.795840</td>
<td>1.000000</td>
<td>-0.013529</td>
<td>-0.078453</td>
<td>-0.039648</td>
<td>-0.046795</td>
<td>-0.061197</td>
<td>-0.043269</td>
<td>-0.093295</td>
<td>-0.051414</td>
</tr>
<tr>
<th>co2_1000</th>
<td>0.156284</td>
<td>-0.013529</td>
<td>1.000000</td>
<td>0.459959</td>
<td>0.483094</td>
<td>0.699502</td>
<td>-0.098430</td>
<td>-0.228570</td>
<td>0.639473</td>
<td>0.764942</td>
</tr>
<tr>
<th>cellphone</th>
<td>0.017960</td>
<td>-0.078453</td>
<td>0.459959</td>
<td>1.000000</td>
<td>0.537769</td>
<td>0.401796</td>
<td>-0.071408</td>
<td>-0.092684</td>
<td>0.432711</td>
<td>0.505035</td>
</tr>
<tr>
<th>urbanpop</th>
<td>0.100557</td>
<td>-0.039648</td>
<td>0.483094</td>
<td>0.537769</td>
<td>1.000000</td>
<td>0.607351</td>
<td>-0.123386</td>
<td>-0.110808</td>
<td>0.625057</td>
<td>0.670027</td>
</tr>
<tr>
<th>gdp</th>
<td>0.101115</td>
<td>-0.046795</td>
<td>0.699502</td>
<td>0.401796</td>
<td>0.607351</td>
<td>1.000000</td>
<td>-0.043769</td>
<td>-0.314087</td>
<td>0.761361</td>
<td>0.945029</td>
</tr>
<tr>
<th>fuelwood</th>
<td>-0.096348</td>
<td>-0.061197</td>
<td>-0.098430</td>
<td>-0.071408</td>
<td>-0.123386</td>
<td>-0.043769</td>
<td>1.000000</td>
<td>0.120487</td>
<td>-0.169399</td>
<td>-0.054676</td>
</tr>
<tr>
<th>gini</th>
<td>-0.032174</td>
<td>-0.043269</td>
<td>-0.228570</td>
<td>-0.092684</td>
<td>-0.110808</td>
<td>-0.314087</td>
<td>0.120487</td>
<td>1.000000</td>
<td>-0.423783</td>
<td>-0.275202</td>
</tr>
<tr>
<th>vehicles</th>
<td>0.070360</td>
<td>-0.093295</td>
<td>0.639473</td>
<td>0.432711</td>
<td>0.625057</td>
<td>0.761361</td>
<td>-0.169399</td>
<td>-0.423783</td>
<td>1.000000</td>
<td>0.776197</td>
</tr>
<tr>
<th>gdp_ppp</th>
<td>0.116638</td>
<td>-0.051414</td>
<td>0.764942</td>
<td>0.505035</td>
<td>0.670027</td>
<td>0.945029</td>
<td>-0.054676</td>
<td>-0.275202</td>
<td>0.776197</td>
<td>1.000000</td>
</tr>
</tbody>
</table>
</div>
<p>As we can see from the correlation matrix, plain CO<sub>2</sub> emission levels are heavily correlated with population and almost unrelated to the rest. When we discount the population factor by normalizing to get CO<sub>2</sub> emission per 1000 people, we find that it somewhat correlated with cellphone connections per 1000 person and urban population percentage. It is well correlated with gdp, vehicles per 1000and gpd_ppp. Now lets try plotting it.</p>
<pre><code class="language-python">import matplotlib.pyplot as plt
fig = plt.figure(tight_layout=True)
fields = ['cellphone', 'urbanpop', 'gdp', 'fuelwood', 'gini', 'vehicles', 'gdp_ppp']
fig.set_size_inches(12,12)
for index, field in enumerate(fields):
ax = fig.add_subplot(3, 3, index+1, xlabel=field, ylabel='CO&lt;sub&gt;2&lt;/sub&gt; emissions per 1000 people')
ax.scatter(data[field], data['co2_1000'])

fig.show()
</code></pre>
<p>[caption id="attachment_86" align="aligncenter" width="856"]<img class="wp-image-86 size-full" src="/assets/2013/12/co2-viz_14_1-e1620051487397.png" alt="" width="856" height="856" /> Factors that correlate the most with CO2 emmisions.[/caption]</p>
<p>Now lets us remove the records which have NaN as value for CO<sub>2</sub> emmisions</p>
<pre><code class="language-python">clean_data = data[np.isfinite(data['co2_1000'])]
y = clean_data['co2_1000'].values
x = clean_data[['cellphone', 'urbanpop', 'gdp', 'fuelwood', 'gini', 'vehicles', 'gdp_ppp']].values.transpose()
x.shape

</code></pre>
<pre><code class="language-python">(7, 178)

</code></pre>
<p>Now lets see which countries have the highest carbon emission per 1000 people.</p>
<pre><code class="language-python">plt.cool()
sorted_data = clean_data.sort(columns=['co2_1000'], ascending=False)
top50_data = sorted_data[:40]
labels = top50_data.index
co2_1000 = top50_data['co2_1000'].values
figure(figsize=(10,10))
colors = ['yellowgreen', 'gold', 'lightskyblue', 'lightcoral']
colors = ['seashell','pink','plum', 'PapayaWhip','violet', 'PeachPuff','MediumOrchid']
plt.pie(co2_1000, labels=labels, autopct='%1.1f%%', colors=colors, startangle=90)
# Set aspect ratio to be equal so that pie is drawn as a circle.
plt.axis('equal')
plt.show()

</code></pre>
<p><img class="alignnone wp-image-87 size-medium" src="/assets/2013/12/co2-viz_18_1-300x270.png" alt="" width="300" height="270" /></p>
<p>It is interesting to see that the countries that contribute the highest carbon footprint is from middle east followed by the developed countries. So which contribute the least? As expected, countries having the smallest carbon footprint are mostly least developed.</p>
<pre><code class="language-python">', '.join([x.title() for x in sorted_data[-30:].index])

</code></pre>
<pre><code class="language-python">'Kenya, Laos, Ivory Coast, Togo, Sierra Leone, Haiti, Afghanistan, Guinea-Bissau, Comoros, Myanmar, Timor-Leste, Tanzania, Zambia, Liberia, Mozambique, Nepal, Guinea, Uganda, Burkina Faso, Ethiopia, Eritrea, Madagascar, Niger, Rwanda, Malawi, Central African Republic, Chad, Mali, Congo, Burundi'

</code></pre>
<p>Lets make a map of countries and their carbon footprint. We need to map the countries to their 3-letter abbreviations.</p>
<p>Let as look at the distribution of emission amoung the countries. As it is apparent, there is assymmetry in how the carbon emission is distributed.</p>
<pre><code class="language-python">plt.hist(sorted_data['co2_1000'], color=['green'], bins=15)

</code></pre>
<pre><code class="language-python">(array([91, 23, 29, 18, 4, 4, 2, 1, 2, 1, 0, 1, 0, 0, 2]),
array([ 2.17303704e-02, 2.57036546e+00, 5.11900054e+00,
7.66763563e+00, 1.02162707e+01, 1.27649058e+01,
1.53135409e+01, 1.78621760e+01, 2.04108111e+01,
2.29594461e+01, 2.55080812e+01, 2.80567163e+01,
3.06053514e+01, 3.31539865e+01, 3.57026216e+01,
3.82512567e+01]),
&lt;a&gt;)&lt;/a&gt;

</code></pre>
<p>[caption id="attachment_88" align="aligncenter" width="380"]<img class="wp-image-88 size-full" src="/assets/2013/12/co2-viz_25_1-e1620051525738.png" alt="Biggest Absolute contributors of CO2 emissions" width="380" height="257" /> Biggest Absolute contributors of CO2 emissions[/caption]</p>
<p>This begs the question, who are the biggest absolute contributors?</p>
<pre><code class="language-python">carbon_absolute = clean_data.sort(columns=['value'], ascending=False)
total_emission = sum(carbon_absolute['value'])
print 'Total Global Emission is:', total_emission
carbon_absolute['percentage'] = 100 * carbon_absolute['value']/total_emission
print carbon_absolute['percentage'][:10]

</code></pre>
<pre><code class="language-python">Total Global Emission is: 29948374.332
country
china 25.667883
usa 17.695662
india 6.609456
russia 5.257000
japan 3.676774
germany 2.452885
iran 2.010311
canada 1.716078
south korea 1.700846
south africa 1.666255
Name: percentage, dtype: float64

</code></pre>
<pre><code class="language-python">plt.hist(carbon_absolute['percentage'], color=['green'], bins=15)

</code></pre>
<pre><code class="language-python">(array([170, 3, 1, 2, 0, 0, 0, 0, 0, 0, 1, 0, 0,
0, 1]),
array([ 1.71421659e-04, 1.71135221e+00, 3.42253300e+00,
5.13371379e+00, 6.84489458e+00, 8.55607537e+00,
1.02672562e+01, 1.19784370e+01, 1.36896177e+01,
1.54007985e+01, 1.71119793e+01, 1.88231601e+01,
2.05343409e+01, 2.22455217e+01, 2.39567025e+01,
2.56678833e+01]),
&lt;a&gt;)&lt;/a&gt;

</code></pre>
<p><img class="aligncenter wp-image-89 size-full" src="/assets/2013/12/co2-viz_28_1-e1620051543841.png" alt="" width="380" height="257" /></p>
<p>It is interesting to see that even though the Middle East have the largest per capita contribution, they are insignificant compared to the giants, China, USA and India because of the sheer population size of the latter. China, USA and India together contribute nearly 50% of the world's carbon emissions. Lets generate a map of the countries of the world by net emissions and by per 1000 people footprint. The code used for generating the required javascript are below the map.</p>
<p><script src="https://d3js.org/d3.v3.min.js"></script><br />
<script src="https://d3js.org/topojson.v1.min.js"></script><br />
<script src="https://cdnjs.cloudflare.com/ajax/libs/datamaps/0.5.8/datamaps.world.min.js"></script></p>
<div id="maps">
<h2>Total Emission Map</h2>
<div style="position: relative; width: 800px; height: 420px;">
<div id="total_emission" style="position: relative; width: 800px; height: 300px;"></div>
</div>
<div style="position: relative; width: 800px; height: 420px;">
<h2 style="display: block; clear: both;">Emission per 1000 People Map</h2>
<div id="co2_1000" style="position: relative; width: 800px; height: 300px;"></div>
</div>
<p><script type="text/javascript"  src="https://staging.minvolai.com/wp-content/uploads/2017/12/total_emission.js"</p>
<p><script type="text/javascript" src="https://staging.minvolai.com/wp-content/uploads/2017/12/co2_1000.js"></script></p>
</div>
<div style="display: block; clear: both;"></div>
<div>And here is the code for generating the maps.</div>
<pre><code class="language-python">def generate_total_emission_map():

def get_fill(carb_em, index):
if carb_em &gt;= 10:
return 'VERYHIGH'
elif carb_em &gt;= 2:
return 'HIGH'
elif carb_em &gt;= 1:
return 'MEDIUM'
else:
return 'LOW'

fills = {'VERYHIGH':'#FF003C',
'HIGH':'#FF8A00',
'MEDIUM':'#FABE28',
'LOW':'#88C100',
'VERYLOW':'#00C176',
'defaultFill':'#87796F'}
matched_abbr = dict([x for x in csv.reader(open('abbr.csv', 'r'))])
map_data = defaultdict(lambda: defaultdict(str))
columns = carbon_absolute.columns

for index, item in enumerate(carbon_absolute.index):
abbr = matched_abbr[item]
map_data[abbr]['rank'] = index + 1
map_data[abbr]['fillKey'] = get_fill(carbon_absolute.ix[item]['percentage'],item)
map_data[abbr]['cname'] = item
map_data[abbr]['population'] = carbon_absolute.ix[item]['pop']/1000
for col in columns:
map_data[abbr][col] = "%2.2f" %carbon_absolute.ix[item][col]

return map_data, fills

#ratio = /max_carbon
#fills[abbr] = ratio_to_color(ratio)

import json
map_data, fills = generate_total_emission_map()
jscode = open('javascript_template.js').read().replace('#FILLS#', json.dumps(fills))\
.replace('#DATA#', json.dumps(map_data))\
.replace('#MAPNAME#', 'total_emission')

open('total_emission.js', 'w').write(jscode)

def generate_co2_1000_map():

def get_fill(carb_em, index):
if carb_em &gt;= 20:
print 'veryhigh', index
return 'VERYHIGH'
elif carb_em &gt;= 10:
return 'HIGH'
elif carb_em &gt;= 5:
return 'MEDIUM'
elif carb_em &gt;= 2:
return 'LOW'
else:
return 'VERYLOW'

fills = {'VERYHIGH':'#FF003C',
'HIGH':'#FF8A00',
'MEDIUM':'#FABE28',
'LOW':'#88C100',
'VERYLOW':'#00C176',
'defaultFill':'#87796F'}
matched_abbr = dict([x for x in csv.reader(open('abbr.csv', 'r'))])
map_data = defaultdict(lambda: defaultdict(str))
columns = carbon_absolute.columns

for index, item in enumerate(carbon_absolute.index):
abbr = matched_abbr[item]
map_data[abbr]['rank'] = index + 1
map_data[abbr]['fillKey'] = get_fill(carbon_absolute.ix[item]['co2_1000'],item)
map_data[abbr]['cname'] = item
map_data[abbr]['population'] = carbon_absolute.ix[item]['pop']/1000
for col in columns:
map_data[abbr][col] = "%2.2f" %carbon_absolute.ix[item][col]

return map_data, fills

map_data, fills = generate_co2_1000_map()
jscode = open('javascript_template.js').read().replace('#FILLS#', json.dumps(fills))\
.replace('#DATA#', json.dumps(map_data))\
.replace('#MAPNAME#', 'co2_1000')

open('co2_1000.js', 'w').write(jscode)

</code></pre>
