# Citation collector

The collector on `master` in `dongzhuoyao/taohu.me` reads the public Google Scholar
profile and publishes a complete snapshot to `google-scholar-stats`. It was
migrated from `dongzhuoyao/acad-homepage` on 2026-11-20, including its last
successful snapshot without changing that snapshot's collection date.
It uses the validated parser from
the Tao Hu website collector and does not require `scholarly` or `bibtexparser`.

The existing endpoints remain compatible:

- `gs_data.json`: `citedby`, `publications[author_pub_id].num_citations`, profile
  details, indices, annual counts, and an `updated` UTC timestamp.
- `gs_data_shieldsio.json`: the original Shields endpoint schema.
- `citations.yml`: the normalized snapshot used by the new website collector.

The current endpoints are:

- https://raw.githubusercontent.com/dongzhuoyao/taohu.me/google-scholar-stats/gs_data.json
- https://raw.githubusercontent.com/dongzhuoyao/taohu.me/google-scholar-stats/gs_data_shieldsio.json
- https://raw.githubusercontent.com/dongzhuoyao/taohu.me/google-scholar-stats/citations.yml

The old repository's stats branch is retained as a historical snapshot; new
updates are published only in this repository.

Run locally from the repository root:

```sh
python3 -m pip install -r google_scholar_crawler/requirements.txt
python3 -m unittest discover -s google_scholar_crawler/tests -v
python3 google_scholar_crawler/main.py --scholar-id EchdyZEAAAAJ
```

The default output directory is `google_scholar_crawler/results/`. Override it
with `--output-dir`. The Scholar ID can also be supplied through the existing
`GOOGLE_SCHOLAR_ID` environment variable; the default is Tao Hu's public ID,
`EchdyZEAAAAJ`. In Actions, a repository variable named `GOOGLE_SCHOLAR_ID`
takes precedence over a secret of the same name, then the public default is
used. No copied secret or personal access token is required: publication uses
this repository's built-in `GITHUB_TOKEN` with `contents: write`.

GitHub Actions tests every collector change and collects on pushes to `master`,
manual workflow runs, and the existing daily schedule (16:00 China time).
Pull requests only run the tests. The workflow checks out the existing stats
branch and appends a commit without force pushing. A blocked request, malformed
page, wrong profile, or incomplete pagination fails the run before publication,
so the branch and the previous successful update date remain unchanged.

Scholar can block requests from GitHub runners. This collector does not bypass
CAPTCHAs or use proxies. In that case, a successful local collection can be
committed to the stats branch; do not replace real data with zero counts.
