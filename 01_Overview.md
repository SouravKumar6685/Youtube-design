# System Design: YouTube (AWS-Centric Overview)

## What is YouTube?
YouTube is a global video-sharing platform enabling:
- **Content Creators**: Upload, monetize, and manage videos.
- **Viewers**: Stream, search, like/dislike, and comment.
- **Key Stats** (2024):
  - 2.5+ billion monthly active users.
  - 694K hours of video streamed per minute.
  - Second-most visited site after Google.

![YouTube Flow](https://example.com/youtube-flow.png)  
*Content creators upload videos; viewers stream them.*

---

## YouTube’s Popularity Drivers
| Factor               | Description                                                                 |
|----------------------|-----------------------------------------------------------------------------|
| **Simple UI**        | Intuitive interface for uploads and streaming.                              |
| **Rich Content**     | Free hosting attracts diverse creators (vloggers, educators, etc.).         |
| **Google Backing**   | Acquired in 2007; leverages Google’s infrastructure and credibility.        |
| **Monetization**     | Partner Program incentivizes creators via ad revenue.                       |
| **Media Partnerships**| Collaborations with CNN, NBC, etc., for premium content.                   |

---

## YouTube’s Growth Metrics
```bash
# Key Statistics (2024)
- Monthly Active Users (MAU): 2.5B+  
- Hours Streamed/Minute: 694,000  
- Storage Needed: 900 GB/min (500 hours uploaded/min).
```