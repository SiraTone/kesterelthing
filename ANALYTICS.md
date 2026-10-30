# Google Analytics setup

1. Go to https://analytics.google.com and create an account and a **property**.
2. Add a **Web data stream** with your site's URL (the domain in `CNAME`).
3. Copy the **Measurement ID** (looks like `G-ABC123XYZ9`) and put it in `config.js` as `gaId`. Commit and push.

Events the site sends (visible under Reports > Realtime, and Engagement > Events after a day or so):
- `page_view` (automatic)
- `stream_play` with `event_name`, `sport`, `channel`
- `like_stream` / `dislike_stream` with `event_name`, `sport`

To read them per event, register `event_name`, `sport` and `channel` as custom dimensions
(Admin > Data display > Custom definitions > Create custom dimension, scope "Event").

Note: likes/dislikes are remembered on the viewer's own device only. Google Analytics can't send counts back to the site,
so there are no public like totals, live viewer counts or admin panel.
