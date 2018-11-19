---
layout: post
title: Jupyter Test
---

# Charting Facebook Messages with Python

I consider myself an intermediate user of Python. However, most of my projects have been web based using libraries such as Flask. So I started looking at one of the other great uses for Python, data analytics and visualisation.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from textblob import TextBlob
import csv
import json

# data = {}

# with open('message.json') as f:
#     data = json.loads(f.read())

# csvwriter = csv.writer(open('test.csv', 'w'), delimiter='\t')
# csvwriter.writerow(["sender", "timestamp", "content", "type"])

# for message in data['messages']:
#     if 'content' in message.keys():
#         csvwriter.writerow([message['sender_name'], message['timestamp_ms'], message['content'], message['type']])


def dateparse(timestamp):
    pd.to_datetime(timestamp, unit='ms')

messages_df = pd.read_csv('test.csv', delimiter='\t', parse_dates=True, date_parser=dateparse)
messages_df['timestamp'] = pd.to_datetime(messages_df['timestamp'], unit='ms')
messages_df['year'] = pd.DatetimeIndex(messages_df['timestamp']).year
messages_df['month'] = pd.DatetimeIndex(messages_df['timestamp']).month
messages_df['month_year'] = pd.to_datetime(messages_df['timestamp']).dt.to_period('M')

messages_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sender</th>
      <th>timestamp</th>
      <th>content</th>
      <th>type</th>
      <th>year</th>
      <th>month</th>
      <th>month_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Samantha Perry</td>
      <td>2018-11-12 16:03:56.886</td>
      <td>What other veg can I throw in?</td>
      <td>Generic</td>
      <td>2018</td>
      <td>11</td>
      <td>2018-11</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Samantha Perry</td>
      <td>2018-11-12 16:03:51.409</td>
      <td>Hahahaha I like to experiment</td>
      <td>Generic</td>
      <td>2018</td>
      <td>11</td>
      <td>2018-11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Tim Thompson</td>
      <td>2018-11-12 16:02:04.704</td>
      <td>That's exactly what I do with Hairy Bikers exc...</td>
      <td>Generic</td>
      <td>2018</td>
      <td>11</td>
      <td>2018-11</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Samantha Perry</td>
      <td>2018-11-12 16:01:43.019</td>
      <td>I've got 6 carrots</td>
      <td>Generic</td>
      <td>2018</td>
      <td>11</td>
      <td>2018-11</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Samantha Perry</td>
      <td>2018-11-12 16:01:37.014</td>
      <td>I've got so many carrots</td>
      <td>Generic</td>
      <td>2018</td>
      <td>11</td>
      <td>2018-11</td>
    </tr>
  </tbody>
</table>
</div>




```python
chart_df = pd.DataFrame({'count' : messages_df.groupby( [messages_df['month_year']] ).size()})
chart_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
    </tr>
    <tr>
      <th>month_year</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-08</th>
      <td>7368</td>
    </tr>
    <tr>
      <th>2016-09</th>
      <td>14825</td>
    </tr>
    <tr>
      <th>2016-10</th>
      <td>17485</td>
    </tr>
    <tr>
      <th>2016-11</th>
      <td>11384</td>
    </tr>
    <tr>
      <th>2016-12</th>
      <td>7361</td>
    </tr>
  </tbody>
</table>
</div>






```python
chart_df = pd.DataFrame({'count' : messages_df.groupby( [messages_df['month_year'], messages_df['sender']] ).size()}).unstack()
chart_df = chart_df[chart_df.index != '2018-11']
chart_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">count</th>
    </tr>
    <tr>
      <th>sender</th>
      <th>Samantha Perry</th>
      <th>Tim Thompson</th>
    </tr>
    <tr>
      <th>month_year</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-08</th>
      <td>4445</td>
      <td>2923</td>
    </tr>
    <tr>
      <th>2016-09</th>
      <td>8627</td>
      <td>6198</td>
    </tr>
    <tr>
      <th>2016-10</th>
      <td>10024</td>
      <td>7461</td>
    </tr>
    <tr>
      <th>2016-11</th>
      <td>7186</td>
      <td>4198</td>
    </tr>
    <tr>
      <th>2016-12</th>
      <td>4568</td>
      <td>2793</td>
    </tr>
  </tbody>
</table>
</div>




```python
ax = chart_df.plot.bar(
    figsize=(40,10), 
    yticks=np.arange(0, 10001, 500), 
    title='Facebook Messages',
    legend=False,
    color=[(1,0.435,0.663), (0,0.718,0.8)])
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['bottom'].set_linewidth(0.5)
ax.spines['left'].set_visible(False)
ax.set_ylabel('messages')
ax.axes.get_xaxis().get_label().set_visible(False)
plt.savefig('messages_comparison.png', dpi=300)
plt.show()
```


![png](output_5_0.png)



```python
# minimalist graph version (art)
ax = chart_df.plot.bar(
    figsize=(40,10), 
    yticks=None, 
    legend=False,
    color=[(1,0.435,0.663), (0,0.718,0.8)])
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['bottom'].set_visible(False)
ax.spines['left'].set_visible(False)
ax.tick_params(
    which='both',
    left=False,
    bottom=False,
    top=False,
    labelbottom=False,
    labelleft=False,
)
ax.axes.get_yaxis().get_label().set_visible(False)
ax.axes.get_xaxis().get_label().set_visible(False)
plt.savefig('graph_art.png', dpi=300, transparent=True)
plt.show()
```


![png](output_6_0.png)
