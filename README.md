# Encore test profiles
Some basic transcoding profiles for testing encores.

## Transcoding profiles
| Name | Description |
| --- | --- |
| program | x264 ABR ladder with five rungs, stereo and surround audio, thumbnails, thumbnail map |
| program-x265 | x265 ABR ladder with five rungs, stereo and surround audio, thumbnails, thumbnail map |
| program-kf | x265 ABR ladder with five rungs, stereo and surround audio, thumbnails, thumbnail map, option to override keyframe injections |
| archive | DNXHD 185Mbit/s encode with pcm audio |
| x264_1080p_slow | x264, 2-pass VBR ~3100kbit/s, 1920x1080, 25fps, 96 frames GOP, preset slow |
| x265_1080p_slow | x265, 2-pass VBR ~2600kbit/s, 10bit, 1920x1080, 25fps, 96 frames GOP, preset slow |
| x264_1080p_medium | x264, 2-pass VBR ~3100kbit/s, 1920x1080, 25fps, 96 frames GOP, preset medium |
| x265_1080p_medium | x265, 2-pass VBR ~2600kbit/s, 10bit, 1920x1080, 25fps, 96 frames GOP, preset medium |

The profiles program, program-x265, and archive are based on the test profiles present in the encore github repository under
[src/test/resources/profile](https://github.com/svt/encore/tree/master/src/test/resources/profile).

## Deployment

These profiles are not fetched at runtime. The Encore image in
[TV4/iris-transcoding](https://github.com/TV4/iris-transcoding) (see its
README, "Encore profiles") clones `main` of this repo at build time and reads
profiles from `file:`, so a merge here takes effect only when that image is
rebuilt:

- **dev1** picks up a merge within roughly 10-25 minutes: a poller in
  iris-transcoding (`encore-profiles-poll.yml`, every 10 minutes) reads the
  commit dev1 last baked off the newest successful image build and dispatches
  `Deploy Encore image on IrisDev1` when `main` here is a different commit.
  Nothing in this repo triggers it - this repo is public and holds no token
  for iris-transcoding.
  The rebuild restarts the dev1 Encore service, which can kill in-flight dev1
  transcodes. To confirm which commit a run baked, look at the
  `encore-profiles-ref` label in its `Build and push image` step inputs.
- **stage** and **prod** are not touched by a merge. A nightly drift check in
  iris-transcoding alerts `#iris-monitor-prod` when they bake something other
  than current `main`; see the iris-transcoding README for how to refresh them.
