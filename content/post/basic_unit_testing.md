+++

categories = ["testing"]
date = "2019-07-03"
tags = ["unit-testing", "testing"]
slug = "basic-unit-testing-me-golang"
title = "Πώς κάνουμε unit-testing"

+++

## Τα βασικά

* Για να τρέξουμε τα  tests μας, χρησιμοποιούμε ένα εργαλείο που λέγεται `go test`.
* To όνομα του test αρχείου θα πρέπει να είναι της μορφής `οτιδήποτε_test.go`.

Οπότε, _γρήγορα-γρήγορα_, μάθατε ήδη ότι τα αρχεία που περιέχουν τεστάκια
για τον κώδικά μας, σηματοδοτούνται από το `_test.go` στο όνομά τους. Αυτά τα αρχεία
ψάχνει να βρει το εργαλείο `go test` και όταν τα βρει τα στέλνει στον compiler προς εκτέλεση.
Χρησιμοποιώντας αυτή την _ειδική_ ονοματολογία, τα αρχεία αυτά είναι απομωνομένα από τον βασικό μας κώδικα, συνεπώς
όταν κάνουμε `go build`, ο compiler αγνοεί πλήρως την υπάρξει αυτών των
`_test.go` αρχείων. Τα προσπερνάει -- σαν να μην υπήρξαν ποτέ. Για αυτό συνήθως, σε ένα
περιβάλλον CI, φροντίζουμε πάντα να εκτελούμε `go test` ως ένα από τα στάδια του
pipeline.

## Πού βρίσκονται τα tests;

Ιδανικά, τα τεστ βρίσκονται στο ίδιο πακέτο με αυτό που υπάρχει ο κώδικας τον
οποίον τεστάρουν. Σ' αυτή την περίπτωση θα πρέπει να τοποθετήσετε το τεστ
στον ίδιο φάκελο μαζί με τον κώδικα σας.

```go
| example (directory)
 \
  \ 
    -- example.go
    -- example_test.go
```

Κατά συνέπεια, τα τεστάκια που θα γράψετε (τα test functions) θα αποτελέσουν
μέρος του ίδιου του πακέτου. Αυτό φαίνεται εξάλλου και από το γεγονός ότι
την ονομασία του πακέτου στο αρχείο μας:

```go
package example
```

Διαφορετικά, αυτό που κάνουν αρκετοί, είναι να χρησιμοποιήσουν ένα τρικ,
προσθέτοντας `_test` στο όνομα του πακέτου:

```go
package example_test
```

Ποιά η διαφορά; 

* Αν θέλετε να γράψετε τεστ για _unexported API_  τότε μπορείτε απλά να χρησιμοποιήσετε το
  ίδιο ακριβώς όνομα του πακέτου, δηλαδή: `package example`. 

* Αν θέλετε να γράψετε τεστ που να προσομοιώνουν το πώς οι χρήστες θα χρησιμοποιούν το API σας
  (προσωπικά προτιμώ αυτή την προσέγγιση) τότε μπορείτε να χρησιμοποιήσετε το `package example_test`.

Συνήθως, όταν γράφουμε ένα καινούριο πακέτο, ξεκινάμε με το _unexported API_. Οπότε ξεκινάμε
να γράφουμε tests με τον πρώτο τρόπο, γιατί θέλουμε να τεστάρουμε το _unexported API_. Στην
συνέχεια, όταν φτάνουμε στο σημείο που γράφουμε το _exported API_ -- το οποίο χρησιμοποιεί το
_unexported API_ -- τότε αρχίζουμε και διαγράφουμε τα _unexported_ τεστ, και ξεκινάμε να
γράφουμε καινούρια _exported_ τεστ. Οπότε, στην τελική, καταλήγουμε στην χρήση την δεύτερης
προσέγγισης. Καθώς προγραμματίζουμε λοιπόν, είναι λογικό να έχουμε περισσότερα _unexported_ τεστ
στην αρχή, τα οποία όμως, τελικά τα μετατρέπουμε σε _exported_ τεστ, χωρίς αυτό να σημαίνει ότι
αλλάζουμε το _testing coverage_ που απαιτείται. Η θεωρία λέει ότι με τα _unexported tests_ ελέγχουμε
τον κώδικα του API μας, και μετα _exported tests_ ελέγχουμε ότι κάποιος άλλος μπορεί να χρησιμοποιήσει
το API μας. Είναι μία ενδιαφέρουσα ισορροπία που μαθένεται με την εμπειρία.

## Verbosity

Τα τεστς τρέχουν συνήθως σε ένα περιβάλλον CI, και όταν αποτυγχάνουν θέλουμε να μπορούμε εύκολα
να καταλάβουμε **τι πήγε στραβά**. Συνεπώς είναι σημαντικό να υπάρχει **κατανοητό output** και να είναι αρκετά
verbose έτσι ώστε να εντοπίσουμε το πρόβλημα γρηγορότερα. Είναι πολύ σημαντικό να μπορεί κάποιος
να διαβάζει τα αποτελέσματα των tests και να μπορεί να συμπεράνει αν κάτι πέτυχε ή απέτυχε.
Τα πιο κλασσικά σύμβολα για κάτι τέτοιο είναι το [check_mark](https://www.fileformat.info/info/unicode/char/2713/index.htm) (με unicode `2713`) και το [ballot ή x](https://www.fileformat.info/info/unicode/char/2717/index.htm) (με unicode `2717`). Οπότε αρχίζουμε
κάπως έτσι:

```go
package example

const (
    succeed = "\u2713"
    failed  = "\u2717"
)
```

### Πώς ονομάζω τις συναρτήσεις των τεστ μου;

Στην συνέχεια ακολουθούν οι συναρτήσεις που κάνουν τον έλεγχο. Είναι πολύ σημαντικό,
αυτές οι functions, να ξεκινούν το όνομά τους με την λέξη `Test` και η επόμενη λέξη
(η οποία συνήθως είναι το όνομα του πράγματος που τεσταρεται)
να ξεκινάει με κεφαλαίο γράμμα. Δηλαδή: `TestDownload`. Αυτό υποδεικνύει ότι η συγκεκριμένη
function είναι _exported_, διαφορετικά το `go test` εργαλείο δεν θα τη βρει. Για παράδειγμα:

```go
func testdownload(t *testing.T) {}  // δεν θα εκτελεστεί ποτέ, γιατί δεν θα την βρει το go test
func Testdownload(t *testing.T) {}  // δεν θα εκτελεστεί ποτέ, γιατί δεν θα την βρει το go test
func Test_Download(t *testing.T) {} // θα εκτελεστεί, αλλά μοιάζει περισσότερο με Python παρά με Go
func TestDownload(t *testing.T) {}  // αυτό είναι το σωστό
```

### Ο ρόλος του T pointer

To δεύτερο πράγμα που προσέχουμε ότι ότι θα πρέπει να δέχεται ως είσοδο έναν _testing T pointer_.
Αυτός ο pointer είναι όλη η ουσία. Το `testing` πακέτο έχει ένα API το οποίο είναι προσβάσιμο
για εμάς μέσω του _T pointer_. Ο pointer αυτός είναι ουσιαστικά το όλο _integration_ με το εργαλείο
`go test`.

### Λίγα πράγματα για το testing API

Μιας που αναφερθήκαμε στον T pointer, που μας κάνει expose το testing API της Go, ας μιλήσουμε λίγο για το τι
μπορούμε να κάνουμε με αυτό:

```go
func (c *T) Error(args ...interface{})
func (c *T) Errorf(format string, args ...interface{})
func (c *T) Fail()
func (c *T) FailNow()
func (c *T) Failed() bool
func (c *T) Fatal(args ...interface{})
func (c *T) Fatalf(format string, args ...interface{})
func (c *T) Helper()
func (c *T) Log(args ...interface{})
func (c *T) Logf(format string, args ...interface{})
func (c *T) Name() string
func (t *T) Parallel()
func (t *T) Run(name string, f func(t *T)) bool
func (c *T) Skip(args ...interface{})
func (c *T) SkipNow()
func (c *T) Skipf(format string, args ...interface{})
func (c *T) Skipped() bool
```

#### log() και logf()
Αυτά που χρησιμοποιούνται κυρίως είναι η `Log()` function της οποίας η δουλειά είναι
να εμφανίσει ένα μύνημα στην οθόνη, το οποίο βελτιώνει το verbosity του τεστ μας. Αυτό
δεν σημαίνει αναγκαστηκά ότι χρησιμοποιείται μόνο για να αιτιολογίσει κάποιο πρόβλημα
ή αποτυχία του τεστ, απλά δίνει επιπλέον πληροφορίες εξηγόντας την συμπεριφορά του κώδικα
που ελέγχουμε.

#### Error() και Errorf()
Μία άλλη συνάρτηση που χρησιμοποιείται συχνά είναι η `Error()` η οποία μας εμφανίζει
και αυτή με την σειρά τις διάφορα μύνημα/πληροφορίες στην οθόνη (όπως και η `Log()`)
αλλά με την διαφορά σηματοδοτεί την αποτυχία του τεστ.Σημαντικό είναι να γνωρίζετε
ότι σε αυτή την περίπτωση (της αποτυχίας), τα επόμενα τεστ θα συνεχίσουν να εκτελούνται.

#### Fatal() και Fatalf()
Τέλος, θα δούμε επίσης και την `Fatal()` η οποία κάνει ακριβώς ότι και `Error()`
με την διαφορά ότι διακόπτει απότομα όλα τα τεστς και δεν εκτελείται περαιτέρω κώδικας. Αυτό
συνήθως γίνεται όταν το σφάλμα είναι τόσο σημαντικό, όπου δεν έχει νόημα να συνεχίσουμε.

Οπότε αυτές οι τρεις functions (`Log`, `Error`, και `Fatal`) είναι σίγουρα οι αγαπημένες μου,
αφού λατρεύω να δίνω πληροφορίες για την συμπεριφορά των tests μου. Η όλη φιλοσοφία του
_unit testing_ είναι να διασφαλίσουμε ότι _κάτι_ (ένα μικρό κομμάτι -- unit) από τον κώδικά
μας _δουλεύει σωστά_. Άσχετα με το αν αυτός κώδικας είναι _exported_ ή _unexported_, το
unit testing θα μας βοήθησει να δούμε αν αυτό το API συμπεριφέρεται σωστά αν και και μόνο
αν του δώσουμε μία συγκεκριμένη _είσοδο_ (input). 

## Παράδειγμα

Ας τεστάρουμε να δούμε αν η `http.Get()` δουλεύει σωστά. Η πιο σωστά, να διασφαλίσουμε
ότι τα _semantics_ της `http.Get()` call δουλεύουν όπως θα περίμενε κανείς. Ας ξεκινήσουμε
δίνοντας κάποιες πληροφορίες:

```go
package example

const (
    succeed = "\u2713"
    failed  = "\u2717"
)

// TestDownload διασφαλίζει ότι η http Get function μπορεί να κάνει download το περιεχόμενο
func TestDownload(t *testing.T) {

}
```

Ας δώσουμε λοιπόν μία διεύθυνση την οποία θα τεστάρει. Θα δώσουμε την διεύθυνση του _RSS feed_ του
δικού μας blog και θα περιμένουμε να πάρουμε απάντηση [200](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/200).

```go
package example

const (
    succeed = "\u2713"
    failed  = "\u2717"
)

// TestDownload διασφαλίζει ότι η http Get function μπορεί να κάνει download το περιεχόμενο
func TestDownload(t *testing.T) {
    url := "https://gopher.gr/index.xml"
    statusCode := 200
}
```

Προσωπικά μας αρέσει η ιδέα της χρήσης [given, when, then](https://martinfowler.com/bliki/GivenWhenThen.html) όταν γράφουμε unit-tests.

* Given: γιατί γράφουμε αυτό το τεστ
* When: τι δεδομένα χρησιμοποιούμε για αυτό το τεστ
* Then: τι πρέπει να συμβεί

Μπορούμε να τα κατακερματίσουμε αυτά σε 3 codeblocks. Θα χρησιμοποιήσουμε λοιπόν
τα `{}` τα οποία ενώ η χρήση τους είναι να ορίσουν ένα καινούριο `scope`, εμείς
θα τα χρησιμοποιήσουμε για _readability_ του τεστ-κώδικά μας. Έτσι μπορούμε να
έχουμε την _Given section_, την _When section_ και την _Then section_ πιο οργανωμένες
μέσα στο αρχείο μας.

* Given: της ανάγκης να τεστάρουμε το κατέβασμα του περιεχομένου μιας σελίδας
* When: δίνουμε αυτην την URL
* Then: περιμένουμε αυτόν τον status code ως απάντηση

Εξίσου σημαντικό είναι να ελέγξουμε τι γίνεται στην περίπτωση που δεν συμβεί αυτό που θέλουμε.
Είναι το ίδιο σημαντικό να ελέγχουμε για σφάλματα.

```go
package example

import (
	"net/http"
	"testing"
)

const (
	succeed = "\u2713"
	failed  = "\u2717"
)

// TestDownload διασφαλίζει ότι η http Get function μπορεί να κάνει download το περιεχόμενο
func TestDownload(t *testing.T) {
	url := "https://gopher.gr/index.xml"
	statusCode := 200

	t.Log("Given the need to test downloading a webpage content")
	{
		t.Logf("\tTest 0:\tWhen checking %q for status code %d", url, statusCode)
		{
			resp, err := http.Get(url)
			if err != nil {
				t.Fatalf("\t%s\tThen it should be able to make the Get call: %v", failed, err)
			}
			t.Logf("\t%s\tThen it should be able to make the Get call.", succeed)

			defer resp.Body.Close()

			if resp.StatusCode == statusCode {
				t.Logf("\t%s\tThen it should receive a %d status code", succeed, statusCode)
			} else {
				t.Errorf("\t%s\tThen it should receive a %d status code: %d", failed, statusCode, resp.StatusCode)
			}
		}
	}
}
```

Όπως βλέπετε υπάρχει αρκετό _code duplication_ και η χρήση των `{}` μπορεί να μην ταιριάζει σε όλους.
Παρόλα αυτά, είναι κάτι που μας αρέσει προσωπικά και το χρησιμοποιούμε. Επίσης προσέξτε πόσο
ίδια είναι η έξοδος για τα μυνήματα που δίνουμε. Αυτό είναι πολύ σημαντικό για το _readability_
όταν θα έχετε πολλά tests και θα θέλετε να βρείτε τι πήγε στραβά. Προσέξτε επίσης την χρήση της
`Fatal()` όπου αν κάτι εκεί στραβά, τότε τερματίζουμε το τεστ μας αμέσως, διότι δεν έχει νόημα να
συνεχίσουμε. Αν όλα πάνε καλά, καλούμε την `defer` για να κλείσουμε το `Body` όπως θα κάναμε κανονικά
στο πραγματικό πρόγραμμα αν καλούσαμε την `http.Get()`. Τέλος έχουμε δύο ελέγχους για την απάντηση
του server, όπου αν δεν είναι _200_ τότε χρησιμοποιούμε την `Errorf()` για να μας δείξει το κατάλληλο
μύνημα σφάλματος.

## Τρέχοντας τα τεστς

#### go test

Ο πρώτος τρόπος να τρέξουμε το test είναι να πάμε στο directory που βρίσκεται το αρχείο-τεστ και να
τρέξουμε το `go test` εργαλείο:

```bash
$ go test
PASS
ok      _/C_/Users/drpan/github/gophergr/content/post   0.580s
```

#### go test -v

Αφού περνάνε όλα τα tests, μας δείχνει `PASS` καθώς επίσης ότι χρειάστηκε _0.580_ δευτερόλεπτα για να τα τρέξει.
Τι γίνεται όμως αν θέλω να δω όλα τα όμορφα μυνήματα που λέγαμε νωρίτερα (ακόμα και
στην περίπτωση που περνάνε όλα τα τεστ); Μπορώ να χρησιμοποιήσω το `-v`:

```bash
$ go test -v
=== RUN   TestDownload
--- PASS: TestDownload (0.10s)
        example_test.go:19: Given the need to test downloading a webpage content
        example_test.go:21:     Test 0: When checking "https://gopher.gr/index.xml" for status code 200
        example_test.go:27:     ✓       Then it should be able to make the Get call.
        example_test.go:32:     ✓       Then it should receive a 200 status code
PASS
ok      _/C_/Users/drpan/github/gophergr/content/post   0.158s
```

Τώρα βλέπετε ξεκάθαρα τον λόγω που χρησιμοποιήσαμε _formatting_ για τα μυνήματα εξόδου. Είναι
πολύ πιο εύκολο να διαβάσει κανείς ποιο σενάριο δοκιμάσαμε και τι τεστάραμε. Παρόμοιο
αποτέλεσμα μπορούμε να πετύχουμε και με _3rd party packages_ όπως για παράδειγμα
η [godog](https://github.com/DATA-DOG/godog) (διαβάστε το
[άρθρο](https://gopher.gr/testarontas-bdd-me-godog/)), αλλά ποιός ο λόγος να βάζουμε
επιπλέον _dependencies_ στον κώδικά μας;

#### Failure

Ας δοκιμάσουμε τώρα να δούμε τι γίνεται στην περίπτωση που το τεστ μας αποτυγχάνει. Για να το
δούμε αυτό θα πρέπει να εισάγουμε μία ψεύτικη τιμή επίτηδες, όπου ξέρουμε εκ των προτέρων
ότι πρόκειται για σίγουρη αποτυχία. Ένας εύκολος τρόπος να το κάνουμε αυτό είναι να αλλάξουμε το
`statusCode` από _200_ σε μία άλλη τιμή:

```go
    statusCode := 666
```

Ας τρέξουμε τώρα ξανά το τεστ να δούμε τι θα γίνει:

```bash
$ go test
--- FAIL: TestDownload (0.10s)
        example_test.go:19: Given the need to test downloading a webpage content
        example_test.go:21:     Test 0: When checking "https://gopher.gr/index.xml" for status code 666
        example_test.go:27:     ✓       Then it should be able to make the Get call.
        example_test.go:34:     ✗       Then it should receive a 666 status code: 200
FAIL
exit status 1
FAIL    _/C_/Users/drpan/github/gophergr/content/post   0.164s
```

Παρατηρούμε λοιπόν ότι στην περίπτωση του σφάλματος, το εργαλείο `go tool` πρόσθεσε αυτόματα
την παράμετρο `-v` έτσι ώστε να μας δώσει περισσότερες πληροφορίες. Πάρα πολύ ωραία!

#### go test -run

Τέλος, θα εξετάσουμε τι γίνεται στην περίπτωση που θέλουμε να τρέξουμε μόνο ένα από τα
επιμέρους tests μας. Για να το κάνουμε αυτό θα χρησιμοποιήσουμε το `go test -run <regular expression>`.
Για παράδειγμα, αν θέλουμε να τρέξουμε όλες τις συναρτήσεις Test μας η οποίες ξεκινάμε με την
λέξη _Down_, δηλαδή `testDown*` τότε θα κάνουμε:

```bash
$ go test -run Down
--- FAIL: TestDownload (0.10s)
        example_test.go:19: Given the need to test downloading a webpage content
        example_test.go:21:     Test 0: When checking "https://gopher.gr/index.xml" for status code 666
        example_test.go:27:     ✓       Then it should be able to make the Get call.
        example_test.go:34:     ✗       Then it should receive a 666 status code: 200
FAIL
exit status 1
FAIL    _/C_/Users/drpan/github/gophergr/content/post   0.161s
```

Οπότε, όπως βλέπετε είναι σημαντικό να ξέρουμε επίσης και τα ονόματα των συναρτήσεων μας, έτσι
ώστε να τις φιλτράρουμε κατά το testing μας. Αυτά για σήμερα και καλό testing :D