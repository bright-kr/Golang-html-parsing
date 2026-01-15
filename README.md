# How to Parse HTML With Golang?

[![Promo](https://github.com/bright-kr/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.co.kr/) 

[Node Parser](https://www.npmjs.com/package/node-html-parser), [Tokenizer](https://github.com/greim/html-tokenizer), 그리고 Goquery, Colly, [Bright Data's Web Scrapers](https://brightdata.co.kr/products/web-scraper) 같은 서드파티 도구를 사용하여 Go에서 HTML 파싱을 마스터하고, 효율적인 [web scraping](https://github.com/bright-kr/Awesome-Web-Scraping)을 수행하십시오.

## Prerequisites

시작하기 전에 Go(Golang)와 Web스크레이핑 개념에 대한 기본적인 이해가 있으면 도움이 됩니다. 머신에 Go가 설치되어 있는지 확인하십시오. 그런 다음 새 프로젝트 폴더를 만들고 다음 명령으로 초기화하십시오:

```bash
mkdir goparser
cd goparser
go mod init goparser
```

다음으로 설정을 테스트하십시오:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

파일을 실행하십시오:

```bash
go run main.go
```

의존성을 설치하십시오:

```bash
go get golang.org/x/net/html
```

## Extracting Data With Node Parser

Node Parser를 사용하면 HTML 페이지의 DOM(Document Object Model)을 순회할 수 있습니다. 이는 인용문과 작성자 같은 특정 요소를 추출하는 데 유용합니다. 다음은 Node Parser를 사용하여 이를 수행하는 방법의 예시입니다:

```go
package main

import (
    "fmt"
    "net/http"
    "golang.org/x/net/html"
)

func main() {
    resp, _ := http.Get("http://quotes.toscrape.com")
    defer resp.Body.Close()
    doc, _ := html.Parse(resp.Body)

    var processNode func(*html.Node)
    processNode = func(n *html.Node) {
        if n.Type == html.ElementNode && n.Data == "span" {
            for _, a := range n.Attr {
                if a.Key == "class" && a.Val == "text" {
                    fmt.Println("Quote:", n.FirstChild.Data)
                }
            }
        }
        if n.Type == html.ElementNode && n.Data == "small" {
            for _, a := range n.Attr {
                if a.Key == "class" && a.Val == "author" {
                    fmt.Println("Author:", n.FirstChild.Data)
                }
            }
        }
        for c := n.FirstChild; c != nil; c = c.NextSibling {
            processNode(c)
        }
    }
    processNode(doc)
}
```

## Extracting Data With Tokenizer

Tokenizer는 HTML 페이지를 토큰 스트림으로 처리하며, 토큰은 HTML의 개별 구성 요소(예: 태그, 속성, 텍스트)를 나타냅니다. 이 접근 방식은 대용량 페이지에서 메모리 효율이 더 좋지만, 더 많은 수동 처리가 필요합니다. 다음은 Tokenizer를 사용하여 인용문과 작성자를 추출하는 예시입니다:

```go
package main

import (
    "fmt"
    "net/http"
    "strings"
    "golang.org/x/net/html"
)

func main() {
    resp, _ := http.Get("http://quotes.toscrape.com")
    defer resp.Body.Close()
    tokenizer := html.NewTokenizer(resp.Body)

    inQuote := false
    inAuthor := false

    for {
        tt := tokenizer.Next()
        switch tt {
        case html.ErrorToken:
            return
        case html.StartTagToken:
            t := tokenizer.Token()
            if t.Data == "span" {
                for _, a := range t.Attr {
                    if a.Key == "class" && a.Val == "text" {
                        inQuote = true
                    }
                }
            }
            if t.Data == "small" {
                for _, a := range t.Attr {
                    if a.Key == "class" && a.Val == "author" {
                        inAuthor = true
                    }
                }
            }
        case html.TextToken:
            if inQuote {
                fmt.Println("Quote:", strings.TrimSpace(tokenizer.Token().Data))
                inQuote = false
            }
            if inAuthor {
                fmt.Println("Author:", strings.TrimSpace(tokenizer.Token().Data))
                inAuthor = false
            }
        }
    }
}
```

## Third Party Alternatives

- **Goquery**: jQuery의 Go 대안으로, DOM 순회 및 CSS 셀렉터를 지원합니다.
- **htmlquery**: Goquery와 유사하지만 XPath 셀렉터를 사용합니다.
- **Colly**: Go를 위한 완전한 형태의 Web스크레이핑 프레임워크입니다.
- **Bright Data Web Scraper**: 페이지를 스크레이핑하고 데이터를 JSON 형식으로 반환하는 API 서비스입니다.

## Conclusion

이제 Go를 사용하여 HTML을 파싱하는 방법을 알게 되었습니다. 전체 페이지 순회에는 Node Parser를 사용하고, 관련 데이터 파싱에는 Tokenizer를 사용하십시오. 더 많은 기능을 위해 서드파티 도구도 살펴보십시오. 또한 다른 스크레이핑 가이드도 확인해 보십시오:

- [**Amazon**](https://github.com/bright-kr/LinkedIn-Scraper)
- [**LinkedIn**](https://github.com/bright-kr/LinkedIn-Scraper)
- [**Google Maps**](https://github.com/bright-kr/Google-Maps-Scraper)
- [**Google News**](https://github.com/bright-kr/Google-News-Scraper)