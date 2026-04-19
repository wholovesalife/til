# xargs -P N runs things in parallel

you can give `xargs` a `-P N` flag to run N processes at once:

    ls *.mp4 | xargs -P 4 -I {} ffmpeg -i {} -vf scale=1280:-1 small/{}

this runs 4 ffmpeg jobs at once instead of one-by-one. scale N to your cpu cores. pairs nicely with `find ... -print0 | xargs -0 -P N ...` for tricky filenames.
