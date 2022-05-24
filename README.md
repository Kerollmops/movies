# movies

Find the lines in your favorite movies

## Where is the data?

The movies, subtitles and settings of the website are currently on an S3 scaleway bucket named `movies-dot-kerollmops-dot-com`.
It is on my personnal account and I pay for it by myself.

The Meilisearch indexes are stored on the <https://cloud.meilisearch.com> service on an instance named `movies-dot-kerollmops-dot-com`.
It is on my personnal account and I pay for it by myself.

## How to index a new movie

This instruction details the steps you need to add a new movie and to be able
to search into it on <https://movies.kerollmops.com>.

### Send the movie on the S3 storage

You should use `s3cmd` to do so and must not forget to set the `content-type` value.
You can also change the size of the chunks your are sending to seep the transfert time.

```bash
s3cmd put la-classe-americaine.mp4 -m 'video/mp4' s3://movies-dot-kerollmops-dot-com --multipart-chunk-size-mb=125
```

Make sure that the file is public and accessible!

We advise that you also send the subtitle file to the S3 storage, this way you are sure that you don't lose it.

### Fill a new index on the Meilisearch engine

Retrieve the subtitle file, transform it accordingly and send it to the Meilisearch server
in a newly created index. The index name should be clear and be named after the movie name.

The JSON documents should look like the following, the id doesn't matter.
You can use [the vtt2csv crate](https://lib.rs/vtt2csv) to convert it.

_We would like to clean up the HTML tags from the text, it would be better_

```json
{
    "id": "574",
    "start": "1835400",
    "end": "1837600",
    "text": "Protestations"
}
```

### Change the settings of the website

You must update the `index.json` that should be available on the S3 storage.
This file is a JSON file that lists the things that are available to the users, it looks like the following:

```json
[
    {
        "name": "La Classe Américaine",
        "indexName": "la-classe-americaine",
        "movieFile": "la-classe-americaine.mp4",
        "subtitleFile": "la-classe-americaine.vtt"
    }
]
```

Once you updated it on the S3, please make sure that the file is public and accessible!
You will most likely need to change the CORS of the S3 bucket, you can find [an example on how to do so with scaleway and `s3cmd`](https://www.scaleway.com/en/docs/tutorials/s3cmd/#configuring-cors) and the _cors.xml_ file in this repository.
