from flask import Flask, render_template_string
import requests

app = Flask(__name__)

# Dein YouTube API-Schlüssel (bereits von dir gegeben)
api_key = 'AIzaSyC4xkahJ59nnIs-1xIGuwYGgAaAK7TWBXw'  # Dein YouTube API-Schlüssel
channel_id = 'UC_x5XG1OV2P6uZZ5bJ6cA'  # ID für den offiziellen YouTube-Kanal (kann geändert werden)

# URL für die YouTube API, um die neuesten Videos zu bekommen
url = "https://www.googleapis.com/youtube/v3/search"
params = {
    'part': 'snippet',
    'channelId': channel_id,
    'order': 'date',  # Sortiert nach den neuesten Videos
    'maxResults': 5,  # Höchstens 5 Videos abrufen
    'key': api_key
}

@app.route('/')
def index():
    # Abrufen der neuesten Videos vom YouTube-Kanal
    response = requests.get(url, params=params)
    data = response.json()

    videos = []
    if 'items' in data:
        for item in data['items']:
            title = item['snippet']['title']
            video_id = item['id'].get('videoId', None)
            if video_id:
                video_url = f"https://www.youtube.com/embed/{video_id}"  # YouTube Embed-URL
                videos.append({'title': title, 'url': video_url})

    # HTML zur Darstellung der Videos im Webbrowser (YouTube-Design eingebettet)
    return render_template_string("""
        <html>
            <head><title>Latest YouTube Videos</title></head>
            <body>
                <h1>Latest Videos from YouTube Channel</h1>
                <div style="display: flex; flex-wrap: wrap; gap: 10px;">
                    {% for video in videos %}
                        <div style="width: 560px; height: 315px;">
                            <h3>{{ video.title }}</h3>
                            <iframe width="560" height="315" src="{{ video.url }}" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
                        </div>
                    {% endfor %}
                </div>
            </body>
        </html>
    """, videos=videos)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=3000) 
