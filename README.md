# Human-activity-recognition-mm
# packages

# standard
import numpy as np
import pandas as pd
import time

# plots
import matplotlib.pyplot as plt
import plotly.express as px
import seaborn as sns
# load data
t1 = time.time()
df = pd.read_csv('act.csv')
t2 = time.time()
print('Elapsed time [s]:', np.round(t2-t1,4))
# structure of data
df.info()
# eval activities
activity_stats = df.activity.value_counts()
activities = activity_stats.index.tolist()
activity_stats.plot(kind='bar')
plt.grid()
plt.show()
print(activity_stats)
# activity vs. user
pd.crosstab(df.user, df.activity)
# select user
sel_user = 3
df_user = df[df.user==sel_user]
# distribution plots
n_bins = 100

for act in activities:
    df_temp = df_user.loc[df.activity==act].copy()
    df_temp.reset_index(inplace=True)
    
    fig, (ax1, ax2, ax3) = plt.subplots(3, 1, figsize=(14,6), sharex=True)
    
    ax1.hist(df_temp['x-axis'], bins=n_bins, color='red', alpha=0.5)
    ax1.set_xlim(-20,20)
    ax1.set_title('user=' + str(sel_user) + ' / ' + act + ' - x')
    ax1.grid()
    
    ax2.hist(df_temp['y-axis'], bins=n_bins, color='green', alpha=0.5)
    ax2.set_xlim(-20,20)
    ax2.set_title('user=' + str(sel_user) + ' / ' + act + ' - y')
    ax2.grid()

    ax3.hist(df_temp['z-axis'], bins=n_bins, color='blue', alpha=0.5)
    ax3.set_xlim(-20,20)
    ax3.set_title('user=' + str(sel_user) + ' / ' + act + ' - z')
    ax3.grid()
    
    plt.show()
    # pairwise scatter plots
for act in activities:
    df_temp = df_user.loc[df.activity==act].copy()
    df_temp.reset_index(inplace=True)
    
    # convert time to seconds and start with 0
    t_min = df_temp.timestamp.min()
    df_temp['time_sec'] = (df_temp['timestamp'] - t_min)/1e9
    
    print(act,':')
    sns.pairplot(data = df_temp[['x-axis','y-axis','z-axis']],
                 plot_kws = dict(s=2))
    plt.show()
    stats_by_act = df_user.groupby('activity')[['x-axis','y-axis','z-axis']].std()
stats_by_act
# and a more visual version
sns.heatmap(stats_by_act, annot=True, linewidths=1, linecolor='black')
plt.show()
stats_full = df.groupby(['activity','user'], 
                        as_index=False)[['x-axis','y-axis','z-axis']].std()
# show full output
pd.set_option('display.max_rows', 200)
stats_full
