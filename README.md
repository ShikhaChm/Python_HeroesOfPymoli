## Python_HeroesOfPymoli

from tabulate import tabulate
import json
import pandas as pd
import numpy as np

with open('purchase_data.json') as json_data:
        data = json.load(json_data)
        df = pd.DataFrame(data)

print " Heroes of Pymoli","\n"
print "==================","\n"

# Total Number of Players
playersCount = df.groupby('SN')['Item ID'].nunique().count()
print "**Total Number of Players**", "\n"
print tabulate([[playersCount]], headers=['Total Players'], tablefmt='fancy_grid').encode('utf-8')

#**Purchasing Analysis (Total)**
# Number of Unique Items
uniqueItems = df.groupby('Item ID')['SN'].nunique()
totUniqItems = uniqueItems.count()

# Total Number of Purchases
totPurchases = df['SN'].count()
# Total Revenue
totRevenue = df['Price'].sum()
# Average Purchase Price
avgPurchasePrice = totRevenue/totPurchases

print "\n**Purchasing Analysis (Total)**", "\n"
print tabulate([[totUniqItems,avgPurchasePrice, totPurchases,totRevenue]], headers=['Num of Unique Items', 'Average Price', 'Num of Purchases' ,'Total Revenue'], tablefmt='fancy_grid').encode('utf-8'),"\n"

#**Gender Demographics**
#* Percentage and Count of Male Players
#* Percentage and Count of Female Players
#* Percentage and Count of Other / Non-Disclosed

playerCount = df.groupby('Gender')['SN'].count()
totPlayerCount = df['Gender'].count()
playerPercentages = 100 * (playerCount / totPlayerCount)

print  "\n**Gender Demographics**", "\n"
print tabulate([[playerPercentages, playerCount ]], headers=['Percentage of Players', 'Total Count'], tablefmt='fancy_grid').encode('utf-8'),"\n"

#*Purchasing Analysis (Gender)**
# * Purchase Count
# * Average Purchase Price
# * Total Purchase Value
# * normalized totals
genderPur = df.groupby('Gender').agg({'Price':['count','mean','sum']})
print "\n**Analysis (Gender)**","\n"
print tabulate(genderPur,
        headers=['Purchase Count', 'Average Purchase Price', 'Total Purchase Value'],
        tablefmt='fancy_grid').encode('utf-8'),"\n"

#**Age Demographics**
#* The below each broken into bins of 4 years (i.e. &lt;10, 10-14, 15-19, etc.)
#  * Purchase Count
#  * Average Purchase Price
#  * Total Purchase Value
#  * Normalized Totals
bins = range(6,50,4)
ageDemogBins = df.groupby(pd.cut(df.Age,bins)).agg({'Price':['count','mean','sum']});
ageDemogBins.columns = ['Purchase Count', 'Average Purchase Price', 'Total Purchase Value'];
ageDemogBins['Normalized Totals'] = ageDemogBins['Total Purchase Value']/np.sum(ageDemogBins['Total Purchase Value'])

print "\n**Age Demographics**", '\n'
print tabulate(ageDemogBins,
        headers=['Age Range','Purchase Count', 'Average Purchase Price', 'Total Purchase Value'],
        tablefmt='fancy_grid').encode('utf-8'),"\n"

#* Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
#    * SN
#    * Purchase Count
#    * Average Purchase Price
#    * Total Purchase Value

topSpenders = df.groupby('SN').agg({'Price':['count','mean','sum']});
topSpenders.columns = ['Purchase Count', 'Average Purchase Price', 'Total Purchase Value'];
topSpenders =topSpenders.sort_values(by = 'Total Purchase Value', ascending=False).head(5)

print "\n**Top Spenders**", "\n"
print tabulate(topSpenders, headers=['SN','Purchase Count', 'Average Purchase Price', 'Total Purchase Value'], tablefmt='fancy_grid').encode('utf-8'),"\n"

#**Identify the 5 most popular items by purchase count, then list (in a table):
#  * Item ID
#  * Item Name
#  * Purchase Count
#  * Item Price
#  * Total Purchase Value

pc = df.groupby('Item ID').agg({'Price':['count','mean','sum']});
pc.columns = ['Purchase Count', 'Average Purchase Price', 'Total Purchase Value'];
pc.sort_values(by = 'Total Purchase Value', ascending=False).head(5)

pc = df.groupby('Item ID').agg({'Item Name': 'first','Price':['count','first','sum']})
pc.columns = ['Purchase Count','Item Price','Total Purchase Value','Item Name']
pc = pc[['Item Name','Purchase Count','Item Price','Total Purchase Value']]
popItems_PurCount = pc.sort_values(by = 'Purchase Count',ascending=False).head(5)

print "\n**Most Popular Items by Purchase**", "\n"
print tabulate(popItems_PurCount, headers=['Item ID','Item Name','Purchase Count','Item Price','Total Purchase Value'], tablefmt='fancy_grid').encode('utf-8'),"\n"

#* Identify the 5 most profitable items by total purchase value, then list (in a table):
#  * Item ID
#  * Item Name
#  * Purchase Count
#  * Item Price
#  * Total Purchase Value

pc = pc[['Item Name','Purchase Count','Item Price','Total Purchase Value']]
profItems_PurCount = pc.sort_values(by = 'Total Purchase Value',ascending=False).head(5)

print "\n**Most Profitable Items**", "\n"
print tabulate(profItems_PurCount, headers=['Item ID','Item Name','Purchase Count','Item Price','Total Purchase Value'], tablefmt='fancy_grid').encode('utf-8'),"\n"
