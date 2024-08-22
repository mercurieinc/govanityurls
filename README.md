# Go Vanity URLs

Go Vanity URLs is a simple Go server that allows you
to set custom import paths for your Go packages.
It runs on Google Run Service.

## Quickstart

Install and run the binary:

```
go get -u pkg.mercurie.ng/govanityurls
# update vanity.yaml
govanityurls
# open http://localhost:8080
```


Get the application:

```
git clone https://github.com/mercurieinc/govanityurls
cd govanityurls
```

Edit `vanity.yaml` to add any number of git repos. E.g., `pkg.mercurie.com/aba` will
serve the [https://github.com/mercurieinc/aba](https://github.com/mercuriein/aba) repo.

```
paths:
  /aba:
    repo: https://github.com/mercurieinc/aba

```

You can add as many rules as you wish.

Deploy the app to cloud run:

```
gcloud run deploy govanityurls --source . 
```

That's it! You can use `go get` to get the package from your custom domain.

```
go get pkg.mercurie.com/aba
```

This project is a normal Go HTTP server, so you can also incorporate the
handler into larger Go servers.

## Configuration File

```
host: example.com
cache_max_age: 3600
paths:
  /foo:
    repo: https://github.com/example/foo
    display: "https://github.com/example/foo https://github.com/example/foo/tree/master{/dir} https://github.com/example/foo/blob/master{/dir}/{file}#L{line}"
    vcs: git
```

<table>
  <thead>
    <tr>
      <th scope="col">Key</th>
      <th scope="col">Required</th>
      <th scope="col">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row"><code>cache_max_age</code></th>
      <td>optional</td>
      <td>The amount of time to cache package pages in seconds.  Controls the <code>max-age</code> directive sent in the <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control"><code>Cache-Control</code></a> HTTP header.</td>
    </tr>
    <tr>
      <th scope="row"><code>host</code></th>
      <td>optional</td>
      <td>Host name to use in meta tags.  If omitted, uses the App Engine default version host or the Host header on non-App Engine Standard environments.  You can use this option to fix the host when using this service behind a reverse proxy or a <a href="https://cloud.google.com/appengine/docs/standard/go/how-requests-are-routed#routing_with_a_dispatch_file">custom dispatch file</a>.</td>
    </tr>
    <tr>
      <th scope="row"><code>paths</code></th>
      <td>required</td>
      <td>Map of paths to path configurations.  Each key is a path that will point to the root of a repository hosted elsewhere.  The fields are documented in the Path Configuration section below.</td>
    </tr>
  </tbody>
</table>

### Path Configuration

<table>
  <thead>
    <tr>
      <th scope="col">Key</th>
      <th scope="col">Required</th>
      <th scope="col">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row"><code>display</code></th>
      <td>optional</td>
      <td>The last three fields of the <a href="https://github.com/golang/gddo/wiki/Source-Code-Links"><code>go-source</code> meta tag</a>.  If omitted, it is inferred from the code hosting service if possible.</td>
    </tr>
    <tr>
      <th scope="row"><code>repo</code></th>
      <td>required</td>
      <td>Root URL of the repository as it would appear in <a href="https://golang.org/cmd/go/#hdr-Remote_import_paths"><code>go-import</code> meta tag</a>.</td>
    </tr>
    <tr>
      <th scope="row"><code>vcs</code></th>
      <td>required if ambiguous</td>
      <td>If the version control system cannot be inferred (e.g. for Bitbucket or a custom domain), then this specifies the version control system as it would appear in <a href="https://golang.org/cmd/go/#hdr-Remote_import_paths"><code>go-import</code> meta tag</a>.  This can be one of <code>git</code>, <code>hg</code>, <code>svn</code>, or <code>bzr</code>.</td>
    </tr>
  </tbody>
</table>


## Git and GO PRIVATE updates
To ensure git is able to correctly use https, update your gitconfig

`git config --global --add url."<git@github.com>:".insteadOf "<https://github.com/>"`

Since the repos are private, update your Go ENV

```bash
go env -w GOPRIVATE=pkg.mercurie.ng/*,pkg.mercurie.com/*,github.com/mercurieinc/* 
```

AND/OR update your `~/.zshrc` file


```bash
  export GOPRIVATE=pkg.mercurie.ng/*,pkg.mercurie.com/*,github.com/mercurieinc/* 
```
