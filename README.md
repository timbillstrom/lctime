# lctime (fork!)

Finally, simple, familiar, locale-based datetime formatting.


## Install

```
go get -u github.com/timbillstrom/lctime
```

## Usage

```go
// Default locale is based on env vars or en_US if none are set.
fmt.Println(lctime.Strftime("%c", time.Now()))
// prints: Mon 14 Dec 2015 10:31:56 PM PST

lctime.SetLocale("es_MX")
fmt.Println(lctime.Strftime("%c", time.Now()))
// prints: lun 14 dic 2015 22:31:56 PST
```

This is very easy if your application only has to translate to a single language.

### Multiple Locales

To do translation to multiple languages without having collisions, you can use 
the [`StrftimeLoc`](https://godoc.org/github.com/variadico/lctime#StrftimeLoc) function. 
It allows you to specify a locale along your time to be translated.

```go
	t := time.Date(2015, 12, 25, 3, 2, 1, 0, time.UTC)
	txt, err := StrftimeLoc("es_MX", "%A, %d de %B de %Y", t)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(txt)

	// Prints viernes, 25 de diciembre de 2015
```

If you need to do several translations or need to pass your localizer as a parameter, you can use
the [`NewLocalizer`](https://godoc.org/github.com/variadico/lctime#NewLocalizer) function.
It allows you to localize several strings to the same language without having to specify it every time.

```go
	t := time.Date(2015, 12, 25, 3, 2, 1, 0, time.UTC)
	l, err := NewLocalizer("da_DK")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(l.Strftime("%A den %d. %B %Y", t))

	// Prints: fredag den 25. december 2015
```

## The problem with the Go standard library

Go's standard library `time` is fine most of the time. However, it's currently
impossible for the Go standard library to format dates based on a user's locale
or langauge setting. Unfortunately, that means code like this, doesn't work.

```go
d := time.Date(2015, 12, 25, 3, 2, 1, 0, time.UTC)
fmt.Println(d.Format("lunes, 02 de enero de 2006"))
// got:  lunes, 25 de enero de 2015
// want: viernes, 25 de diciembre de 2015
```

More importantly, it also means that non-English speakers will either be stuck
with purely numeric dates or dates in a language they don't prefer.

## The solution offered by lctime

`lctime` brings the familiar `setlocale` and `strftime` functions found in other
programming languages like C, Python, or PHP. The formats and translations used
for this package are loosely based on [glibc locale files], with close to 300
locales.

Locale data is just Go code, generated by [go-bindata]. This means you don't
have to worry about shipping anything extra. Just import and use `lctime` like
any other package and `go build` like normal. Everything will just work.

[glibc locale files]: http://lh.2xlibre.net/
[go-bindata]: https://github.com/jteeuwen/go-bindata
