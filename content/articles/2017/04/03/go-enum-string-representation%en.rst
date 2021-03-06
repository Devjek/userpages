[Golang] Enum and String Representation
#######################################

:date: 2017-04-03T08:59+08:00
:tags: Go, Golang, Go enum, go generate, Go stringer command
:category: Go
:summary: Example of Go enumerated constants using *iota* enumerator, and print
          the string representation of enum name.
:og_image: http://www.unixstickers.com/image/cache/data/stickers/golang/golang.sh-600x600.png
:adsu: yes

Recently I use Go enums [2]_ [4]_ to represent the type of webpage:

.. code-block:: go

  package lib

  // Type of the webpage, determined according to path of URL
  type PageType int

  const (
  	RootPage PageType = iota
  	AboutPage
  	WordPage
  	NoSuchPage
  )

  // Determine the type of the webpage according to path of URL.
  func DeterminePageType(urlpath string) PageType
  	// implementation ignored
  }

After reading the discussion of reddit, I found that I can use `go:generate`_
and stringer_ command to indirectly get the string representation of enumerated
type (i.e., enum name).

First install *stringer* command:

.. code-block:: bash

  $ go get -u golang.org/x/tools/cmd/stringer

Give hint to *go generate* command in the code by adding the comment:

.. code-block:: go

  //go:generate stringer -type=PageType

So the code will become:

.. code-block:: go

  package lib

  // Type of the webpage, determined according to path of URL
  //go:generate stringer -type=PageType
  type PageType int

  const (
  	RootPage PageType = iota
  	AboutPage
  	WordPage
  	NoSuchPage
  )

  // Determine the type of the webpage according to path of URL.
  func DeterminePageType(urlpath string) PageType
  	// implementation ignored
  }

.. adsu:: 2

Run *go generate* command in the directory of the source code:

.. code-block:: bash

  $ go generate

A file named ``pagetype_string.go`` will be generated automatically. The content
of ``pagetype_string.go`` is:

.. code-block:: go

  // Code generated by "stringer -type=PageType"; DO NOT EDIT.

  package lib

  import "fmt"

  const _PageType_name = "RootPageAboutPageWordPageNoSuchPage"

  var _PageType_index = [...]uint8{0, 8, 17, 25, 35}

  func (i PageType) String() string {
  	if i < 0 || i >= PageType(len(_PageType_index)-1) {
  		return fmt.Sprintf("PageType(%d)", i)
  	}
  	return _PageType_name[_PageType_index[i]:_PageType_index[i+1]]
  }

An example usage of string representation of enum is to print the enum name if
something goes wrong in the testing code:

.. code-block:: go

  package lib

  import "testing"

  func TestDeterminePageType(t *testing.T) {
  	if pt := DeterminePageType("/"); pt != RootPage {
  		t.Error("wrong type: ", pt.String())
  	}
  }

.. adsu:: 3

----

Tested on: ``Go 1.8``, ``Ubuntu Linux 16.10``

----

References:

.. [1] | `proposal: add typed enum support · Issue #19814 · golang/go : golang <https://www.reddit.com/r/golang/comments/62vn0t/proposal_add_typed_enum_support_issue_19814/>`_
       | `proposal: add typed enum support · Issue #19814 · golang/go · GitHub <https://github.com/golang/go/issues/19814>`_
.. [2] | `What is an idiomatic way of representing enums in Go? - Stack Overflow <http://stackoverflow.com/questions/14426366/what-is-an-idiomatic-way-of-representing-enums-in-go>`_
       | `Iota - The Go Programming Language Specification - The Go Programming Language <https://golang.org/ref/spec#Iota>`_

.. [3] | `How to print the string representation of an "enum" in Go? - Stack Overflow <http://stackoverflow.com/questions/30177344/how-to-print-the-string-representation-of-an-enum-in-go>`_
       | `stringer - GoDoc <https://godoc.org/golang.org/x/tools/cmd/stringer>`_
       | `go - Is it possible to get Enum name without creating String() in Golang - Stack Overflow <http://stackoverflow.com/questions/27187132/is-it-possible-to-get-enum-name-without-creating-string-in-golang>`_
.. [4] `Constants - Effective Go - The Go Programming Language <https://golang.org/doc/effective_go.html#constants>`_
.. [5] | `golang enumeration - Google search <https://www.google.com/search?q=golang+enumeration>`_
       | `golang enumeration - DuckDuckGo search <https://duckduckgo.com/?q=golang+enumeration>`_
       | `golang enumeration - Ecosia search <https://www.ecosia.org/search?q=golang+enumeration>`_
       | `golang enumeration - Qwant search <https://www.qwant.com/?q=golang+enumeration>`_
       | `golang enumeration - Bing search <https://www.bing.com/search?q=golang+enumeration>`_
       | `golang enumeration - Yahoo search <https://search.yahoo.com/search?p=golang+enumeration>`_
       | `golang enumeration - Baidu search <https://www.baidu.com/s?wd=golang+enumeration>`_
       | `golang enumeration - Yandex search <https://www.yandex.com/search/?text=golang+enumeration>`_
.. [6] `Generating code - The Go Blog <https://blog.golang.org/generate>`_

.. _go\:generate: https://www.google.com/search?q=go:generate
.. _stringer: https://godoc.org/golang.org/x/tools/cmd/stringer
