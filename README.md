```python
pip install matplotlib seaborn plotly pandas numpy
```


```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt 
import seaborn as sns
import plotly.io as pio
pio . renderers . default = 'notebook'
```

```python
# CSV dosyasının yolu
dosya_yolu = 'spotify_youtube.csv'

# CSV dosyasını okuma
veri = pd.read_csv("C:\spotify_youtube.csv")

# İlk birkaç satırı görüntüleme
print(veri.head())
```

```python
# Boş sütunları kontrol etme
df.isna().sum()
```

```python
 
# Eksik değerlerin sayısını hesaplama
missing_values_count = df.isna().sum()

# Özel renkler tanımlama
colors = ['#1f77b4', '#ff7f0e', '#2ca02c', '#d62728', '#9467bd', '#8c564b', '#e377c2', '#7f7f7f', '#bcbd22', '#17becf']

# Bar grafiği oluşturma
plt.figure(figsize=(10, 6))
ax = missing_values_count.plot(kind='bar', color=colors[:len(missing_values_count)])  # Her sütun için renk

# Grafik başlıkları ve etiketler
plt.title('Missing Values Count for Each Column')
plt.xlabel('Columns')
plt.ylabel('Number of Missing Values')
plt.xticks(rotation=45, ha='right')

# Her çubuğun üstüne veri etiketlerini ekleme
for p in ax.patches:
    ax.annotate(f'{int(p.get_height())}',  # Ham sayı formatında etiketleme
                (p.get_x() + p.get_width() / 2., p.get_height()),  # Etiketin yeri
                ha='center', va='center', xytext=(0, 10), textcoords='offset points')

plt.tight_layout()
plt.show()
```

```python
df.head()
```

```python
# Performans metriklerini belirleme
performance_metrics = ['Views', 'Likes', 'Comments', 'Stream']

# Her sanatçı için performans metriklerinin toplamını hesaplama
artist_performance = df.groupby('Artist')[performance_metrics].sum()

# Performans skorunu hesaplama (toplam metriklerin toplamı)
artist_performance['Total_Performance'] = artist_performance[performance_metrics].sum(axis=1)

# En iyi sanatçıları sıralama
top_artists = artist_performance.sort_values(by='Total_Performance', ascending=False)

# Sonuçları görüntüleme
print(top_artists[['Total_Performance']].head(10))  # En iyi 10 sanatçıyı gösterir
```

```python
import plotly.express as px
# En iyi 10 sanatçıyı seçme
top_10_artists = top_artists.head(10).reset_index()

# Bar grafiği oluşturma
fig = px.bar(top_10_artists, x='Total_Performance', y='Artist',
             title='Top 10 Artists by Performance',
             labels={'Total_Performance': 'Total Performance', 'Artist': 'Artist'},
             orientation='h')

fig.update_layout(yaxis_title='Artist', xaxis_title='Total Performance')
fig.show()
```


```python
# URL sütunlarının içeriklerine göre platformları ayırmak
df['Platform'] = df.apply(lambda row: 'YouTube' if pd.notna(row['Url_youtube']) else ('Spotify' if pd.notna(row['Url_spotify']) else 'Unknown'), axis=1)

# Performans metriklerini ayırarak gruplama
performance_metrics = ['Views', 'Stream']

# Performans toplamlarını hesaplama
platform_performance = df.groupby(['Artist', 'Platform'])[performance_metrics].sum().reset_index()

# Top 10 sanatçıyı hesaplama (YouTube ve Spotify için ayrı ayrı)
top_artists_youtube = platform_performance[platform_performance['Platform'] == 'YouTube'].sort_values(by='Views', ascending=False).head(10)
top_artists_spotify = platform_performance[platform_performance['Platform'] == 'Spotify'].sort_values(by='Stream', ascending=False).head(10)
# Sonuçları görüntüleme
print("Top 10 YouTube Artists:")
print(top_artists_youtube[['Artist', 'Views']])

print("\nTop 10 Spotify Artists:")
print(top_artists_spotify[['Artist', 'Stream']])
```



```python
import plotly.express as px
from plotly.subplots import make_subplots
import plotly.graph_objects as go

# Subplot oluşturma (1 satır, 2 sütun)
fig = make_subplots(rows=1, cols=2, subplot_titles=("Top 10 YouTube Artists by Views", "Top 10 Spotify Artists by Streams"))

# YouTube için bar grafiğini oluşturma
fig_youtube = px.bar(
    top_artists_youtube,
    x='Artist',
    y='Views',
    text=top_artists_youtube['Views'].apply(lambda x: f'{x:,.0f}')
)

# Spotify için bar grafiğini oluşturma
fig_spotify = px.bar(
    top_artists_spotify,
    x='Artist',
    y='Stream',
    text=top_artists_spotify['Stream'].apply(lambda x: f'{x:,.0f}')
)

# Grafikleri subplota ekleme
fig.add_trace(fig_youtube.data[0], row=1, col=1)
fig.add_trace(fig_spotify.data[0], row=1, col=2)

# Layout ayarları
fig.update_layout(
    height=500, 
    width=1000,
    showlegend=False
)

# Grafikleri gösterme
fig.show()
```

```python
# Top 10 songs based on views
top10_views = df.nlargest(10, 'Views')

# Top 10 songs based on comments
top10_comments = df.nlargest(10, 'Comments')

# Top 10 songs based on likes
top10_likes = df.nlargest(10, 'Likes') 
# Top 10 şarkıların 'Views', 'Likes', 'Comments', ve 'Stream' değerlerini görüntülemek
# 'Views' için en yüksek 10 şarkı
print("Top 10 Songs by Views:")
print(top10_views[['Track', 'Views', 'Likes', 'Comments', 'Stream']])

# 'Comments' için en yüksek 10 şarkı
print("\nTop 10 Songs by Comments:")
print(top10_comments[['Track', 'Views', 'Likes', 'Comments', 'Stream']])

# 'Likes' için en yüksek 10 şarkı
print("\nTop 10 Songs by Likes:")
print(top10_likes[['Track', 'Views', 'Likes', 'Comments', 'Stream']])
```

```python
# En çok izlenen 8 şarkı
fig_views = px.bar(
    top10_views, 
    x='Views', 
    y='Track', 
    orientation='h', 
    title='En Çok İzlenen 8 Şarkı',
    color='Views',
    color_continuous_scale='Blues',
    text='Views'
)
fig_views.update_layout(
    xaxis_title='İzlenme Sayısı',
    yaxis_title='Şarkı'
)
fig_views.show()

# En çok yorum alan 8 şarkı
fig_comments = px.bar(
    top10_comments, 
    x='Comments', 
    y='Track', 
    orientation='h', 
    title='En Çok Yorum Alan 8 Şarkı',
    color='Comments',
    color_continuous_scale='Oranges',
    text='Comments'
)
fig_comments.update_layout(
    xaxis_title='Yorum Sayısı',
    yaxis_title='Şarkı'
)
fig_comments.show()

# En çok beğenilen 7 şarkı
fig_likes = px.bar(
    top10_likes, 
    x='Likes', 
    y='Track', 
    orientation='h', 
    title='En Çok Beğenilen 7 Şarkı',
    color='Likes',
    color_continuous_scale='Greens',
    text='Likes'
)
fig_likes.update_layout(
    xaxis_title='Beğeni Sayısı',
    yaxis_title='Şarkı'
)
fig_likes.show()

```

```python
import plotly.express as px
# En yüksek izlenme sayısına göre 10 şarkıyı görselleştirme
fig_views = px.bar(
    top10_views, 
    x='Track', 
    y=['Views', 'Likes', 'Comments', 'Stream'], 
    title='Top 10 Songs by Views with Additional Metrics',
    labels={'value': 'Count', 'variable': 'Metric'},
    color='variable',
    barmode='group',
    
)

fig_views.update_layout(
    xaxis_title='Track',
    yaxis_title='Count',
    xaxis_tickangle=-45
)

fig_views.show()

```

```python
# Veriyi platforma göre ayırmak
youtube_data = df[df['Platform'] == 'YouTube']
spotify_data = df[df['Platform'] == 'Spotify']

# YouTube: Album_type'e göre en çok izlenen ilk 10 parçayı bulma
top10_viewed_by_album_type_youtube = youtube_data.groupby('Album_type').apply(lambda x: x.nlargest(10, 'Views')).reset_index(drop=True)

# Spotify: Album_type'e göre en çok dinlenen ilk 10 parçayı bulma
top10_streamed_by_album_type_spotify = spotify_data.groupby('Album_type').apply(lambda x: x.nlargest(10, 'Stream')).reset_index(drop=True)

# Sonuçları görüntüleme
print("YouTube - Albüm Tipine Göre En Çok İzlenen İlk 10 Parça:")
print(top10_viewed_by_album_type_youtube[['Track', 'Album_type', 'Views']])

print("\nSpotify - Albüm Tipine Göre En Çok Dinlenen İlk 10 Parça:")
print(top10_streamed_by_album_type_spotify[['Track', 'Album_type', 'Stream']])

```


```python
# YouTube: Albüm Tipine Göre En Çok İzlenen İlk 10 Parçayı Görselleştirme
fig_views = px.bar(
    top10_viewed_by_album_type_youtube,
    x='Views',
    y='Track',
    color='Album_type',
    orientation='h',
    title='Albüm Tipine Göre En Çok İzlenen İlk 10 Parça (YouTube)',
    labels={'Views': 'Toplam İzlenme Sayısı', 'Track': 'Parça', 'Album_type': 'Albüm Tipi'},
    color_discrete_sequence=px.colors.qualitative.Plotly
)

# Spotify: Albüm Tipine Göre En Çok Dinlenen İlk 10 Parçayı Görselleştirme
fig_streams = px.bar(
    top10_streamed_by_album_type_spotify,
    x='Stream',
    y='Track',
    color='Album_type',
    orientation='h',
    title='Albüm Tipine Göre En Çok Dinlenen İlk 10 Parça (Spotify)',
    labels={'Stream': 'Toplam Dinlenme Sayısı', 'Track': 'Parça', 'Album_type': 'Albüm Tipi'},
    color_discrete_sequence=px.colors.qualitative.Plotly
)

# Grafiklerdeki renk karışmasını önlemek için eksenleri ve başlıkları güncelleme
fig_views.update_layout(
    yaxis_title='Parça',
    xaxis_title='Toplam İzlenme Sayısı',
    barmode='stack',
    legend_title='Albüm Tipi',
    margin=dict(l=150, r=50, t=100, b=100)  # Kenar boşlukları ayarlama
)

fig_streams.update_layout(
    yaxis_title='Parça',
    xaxis_title='Toplam Dinlenme Sayısı',
    barmode='stack',
    legend_title='Albüm Tipi',
    margin=dict(l=150, r=50, t=100, b=100)  # Kenar boşlukları ayarlama
)

# Grafikleri Tek Sütunda Gösterme
fig = make_subplots(
    rows=2, 
    cols=1, 
    subplot_titles=("YouTube: En Çok İzlenen", "Spotify: En Çok Dinlenen"),
    row_heights=[0.5, 0.5]  # Her bir grafiğe eşit yükseklik
)

# YouTube grafiğini ekleme
for trace in fig_views.data:
    fig.add_trace(trace, row=1, col=1)

# Spotify grafiğini ekleme
for trace in fig_streams.data:
    fig.add_trace(trace, row=2, col=1)

# Düzenlemeleri Yapma
fig.update_layout(
    height=1200,  # Yüksekliği artırarak grafikleri düzgün şekilde yerleştirme
    width=1000,
    showlegend=True,
    title_text='Albüm Tipine Göre En Çok İzlenen ve Dinlenen İlk 10 Parça',
    title_x=0.5
)

# Grafikleri Gösterme
fig.show()

```
