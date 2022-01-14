# youtube_Data_Cleaning
import numpy as np
import pandas as pd
data=pd.read_csv("BR_youtube_trending_data.csv")
data.columns
Index(['video_id', 'title', 'publishedAt', 'channelId', 'channelTitle',
       'categoryId', 'trending_date', 'tags', 'view_count', 'likes',
       'dislikes', 'comment_count', 'thumbnail_link', 'comments_disabled',
       'ratings_disabled', 'description'],
      dtype='object')
data.isnull().sum()
video_id                0
title                   0
publishedAt             0
channelId               0
channelTitle            0
categoryId              0
trending_date           0
tags                    0
view_count              0
likes                   0
dislikes                0
comment_count           0
thumbnail_link          0
comments_disabled       0
ratings_disabled        0
description          4369
dtype: int64
len(data['video_id'].unique())
16098
Clean DataSet
clean_data=data.copy(deep=True)
#Replace NaN in description with space
clean_data["description"].fillna(" ", inplace=True)
#Delete all rows with missing values if any
clean_data.dropna(inplace=True)
#Primary_key-Unique 'video_id' with largest number of views
primary_key=clean_data.groupby("video_id")["view_count"].idxmax()
#keeping only relevant records
clean_data=clean_data.loc[primary_key]
clean_data.head()
video_id	title	publishedAt	channelId	channelTitle	categoryId	trending_date	tags	view_count	likes	dislikes	comment_count	thumbnail_link	comments_disabled	ratings_disabled	description
97383	--FmExEAsM8	IVE 아이브 'ELEVEN' MV	2021-12-01T09:00:03Z	UCYDmx2Sfpnaxg488yBpZIGg	starshipTV	10	2021-12-07T00:00:00Z	Kpop|girl group|1theK|Starshiptv|starship|뮤비|티...	33000246	851604	14910	54201	https://i.ytimg.com/vi/--FmExEAsM8/default.jpg	False	False	IVE Twitter: https://twitter.com/IVEstarship: ...
12591	--G13b-WWtQ	Ketchupzão tá na pista 🤣🤣🤣🤣🤣	2020-10-05T12:47:00Z	UCUlU4ipnSw0JCX2j7VI0FGg	MC NEGÃO DA BL	23	2020-10-13T00:00:00Z	[None]	1739525	301883	1376	7511	https://i.ytimg.com/vi/--G13b-WWtQ/default.jpg	False	False	Shows/divulgações👉💥 assessoria 21 9 72356832 ...
96764	--G_gEndvlQ	Anitta gritou presente na cerimônia de abertur...	2021-11-27T21:56:57Z	UCyuLjFPzlkMSYJpIpY8M6qA	CONMEBOL Libertadores	17	2021-12-03T00:00:00Z	conmebol libertadores|libertadores|liberta|cop...	285748	11341	2074	2343	https://i.ytimg.com/vi/--G_gEndvlQ/default.jpg	False	False	Inscreva-se no canal oficial da CONMEBOL Liber...
71190	--UrUbZf9fk	Mikezin, Kant | Mamma Mia | Prod. Chiocki	2021-07-16T16:00:14Z	UCi8-sBaCH2F-OH-TBaRtkRA	Mikezin	10	2021-07-27T00:00:00Z	Mikezin|Kant|Chiocki|Mamama miaa|Eu to no corr...	390770	44680	212	1543	https://i.ytimg.com/vi/--UrUbZf9fk/default.jpg	False	False	Vídeo Oficial da faixa Mamma MiaMúsica: Mamma ...
63178	--zMMieVeAQ	Hytalo Santos - Marginal (Clipe Oficial) MK n...	2021-06-05T22:00:11Z	UC5K2cu7OuDEUFWeRKpeSH9A	Hytalo Santos	24	2021-06-16T00:00:00Z	[None]	1995018	196834	5877	0	https://i.ytimg.com/vi/--zMMieVeAQ/default.jpg	True	False	Hytalo Santos - Marginal (Clipe Oficial)Produ...
#delete rows with comments_disabled=True or ratings_disabled=True as we only need to work with comments or ratings which are visible
clean_data=clean_data[(clean_data['comments_disabled']==False) & (clean_data['ratings_disabled']==False)]
#Replace None in tags with space
clean_data.loc[clean_data['tags']=='[None]', 'tags']=' '
    # delete non-ASCII and non-English characters
for text_column in ['title', 'channelTitle', 'description', 'tags']:
        # for all rows in the column apply a filter
        # that only leaves characters from 'printable'
        # since filter does not return a string, then you need to use the join method
        clean_data[text_column] = clean_data[text_column].apply(
            lambda x: ''.join(filter(lambda xi: xi in printable, x)))

    # if there is not a single letter left
    # in the title of the video or in the channel title
    # the video is definitely not in English
    symbols = [c for c in "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"]
    clean_data = clean_data[clean_data['title'].str.contains('|'.join(symbols))]
    clean_data = clean_data[clean_data['channelTitle'].str.contains(
        '|'.join(symbols))]

    # create new empty column
    clean_data['comments'] = " "

    # rename column to match snake case
    clean_data.rename(columns={'channelTitle': 'channel_title',
                             'publishedAt': 'published_at',
                             'channelId': 'channel_id'}, inplace=True)

    # delete non-relevant columns
    clean_data.drop(['categoryId', 'trending_date',
                   'thumbnail_link', 'comments_disabled',
                   'ratings_disabled'], axis=1, inplace=True)

    clean_data = clean_data.reindex(columns=['video_id', 'title', 'channel_id', 'channel_title',
                                         'published_at', 'view_count', 'likes', 'dislikes',
                                         'comment_count', 'tags', 'description', 'comments'])

    clean_data.reset_index(drop=True, inplace=True)
return clean_data
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
~\anaconda3\lib\site-packages\pandas\core\indexes\base.py in get_loc(self, key, method, tolerance)
   2894             try:
-> 2895                 return self._engine.get_loc(casted_key)
   2896             except KeyError as err:

pandas\_libs\index.pyx in pandas._libs.index.IndexEngine.get_loc()

pandas\_libs\index.pyx in pandas._libs.index.IndexEngine.get_loc()

pandas\_libs\hashtable_class_helper.pxi in pandas._libs.hashtable.PyObjectHashTable.get_item()

pandas\_libs\hashtable_class_helper.pxi in pandas._libs.hashtable.PyObjectHashTable.get_item()

KeyError: 'channelTitle'

The above exception was the direct cause of the following exception:

KeyError                                  Traceback (most recent call last)
<ipython-input-76-545609d2f0d1> in <module>
      4     # that only leaves characters from 'printable'
      5     # since filter does not return a string, then you need to use the join method
----> 6     clean_data[text_column] = clean_data[text_column].apply(
      7         lambda x: ''.join(filter(lambda xi: xi in printable, x)))
      8 

~\anaconda3\lib\site-packages\pandas\core\frame.py in __getitem__(self, key)
   2900             if self.columns.nlevels > 1:
   2901                 return self._getitem_multilevel(key)
-> 2902             indexer = self.columns.get_loc(key)
   2903             if is_integer(indexer):
   2904                 indexer = [indexer]

~\anaconda3\lib\site-packages\pandas\core\indexes\base.py in get_loc(self, key, method, tolerance)
   2895                 return self._engine.get_loc(casted_key)
   2896             except KeyError as err:
-> 2897                 raise KeyError(key) from err
   2898 
   2899         if tolerance is not None:

KeyError: 'channelTitle'
 
