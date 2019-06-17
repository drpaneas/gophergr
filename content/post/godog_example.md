+++

categories = ["testing"]
date = "2019-06-17"
tags = ["godog", "testing", "bdd", "cucumber"]
slug = "testarontas-bdd-me-godog"
title = "Τεστάροντας BDD με την Godog"

+++

Είναι ευρέως γνωστό ότι η Go έχει κατά νου το *testing* του κώδικα,
για αυτό και βλέπετε συχνά-πυκνά, ένα `import testing` statement,
που απευθύνεται στο `testing` πακέτο μέρος της βασικής
βιβλιοθήκης της γλώσσας. Σύμφωνα με την λογική των δημιουργών της Go,
ο προγραμματιστής είναι εξίσου υπεύθυνος για την συγγραφή tests,
ενώ ο ρόλος του QA Engineer είναι η αξιολογήση αυτών. Το μεγαλύτερο
πρόβλημα ανάμεσα σε αυτούς του δύο μηχανικούς είναι η low-level γνώση
του κώδικα:

Ο ρόλος του developer είναι πιο κοντά στο κώδικα, γράφοντας κυρίως
unit tests, διασφαλίζοντας την απουσία κάποιων βασικών προγραμματιστικών
προβλημάτων, όπως σωστό κάλεσμα συναρτήσεων, κλπ. Ο δε QA Engineer, είναι
αυτός που κοιτάει τα πράγματα από την μεριά του χρήστη, και ασχολείται
περισσότερο με integration και e2e (end-to-end) tests, τα οποία είναι
συνήθως γραμμένα σε μορφή script (είτε bash είτε python) που τρέχουν
αυτόματα σε κάποιο [Jenkins](https://jenkins.io/projects/blueocean/),
[Concourse](https://concourse-ci.org/) ή κάποιο άλλο CI pipeline (βλ 
[TravisCI](https://travis-ci.org/),
[CircleCI](https://circleci.com/) κλπ).

Τι γίνεται λοιπόν όταν ένα ωραίο πρωί ξυπνάει ο 
[Product Owner](https://www.scrum.org/resources/what-is-a-product-owner) και
ζητάει από τους developers να γράψουν και αυτοί integration tests;
Οι developers επιλέγουν ένα testing framework (πχ [Ginkgo/Gomega](https://onsi.github.io/ginkgo/)) και ο QA Engineer δεν έχει ιδέα από που να το πιάσει και από πού να το αφήσει. Συνεπώς, δημιουργείται ένα *tech dept* μεταξύ των δύο αυτών ρόλων με αποτέλεσμα να ζημιώνεται η ποιότητα του προϊόντος. Την λύση έρχεται
να δώσει η μέθοδος BDD.

## Τι είναι η μέθοδος BDD

Στην περίπτωση που δεν είστε εξικοιωμένοι με την έννοια του **BDD**, επιτρέψτε μου να αναφέρω τι λένε τα βιβλία της *Αγίας Γραφής* (ή αλλιώς Agile):

[Behaviour Driven Development (BDD)](https://en.wikipedia.org/wiki/Behavior-driven_development) είναι μία προσέγγιση ανάπτυξης λογισμικού με τέτοιο τρόπο ώστε να λαμβάνεται σοβαρά υπόψην το feedback των πελατών ή αλλιώς οι στόχοι το προϊόντος (βλέπε Product Manager/Product Owner). Στην ουσία πρόκειται για μία συνεργασία μεταξύ ατόμων που μιλάνε με πελάτες (βλ. product managers, sales engineers, technical strategists) και ατόμων που γράφουν κώδικα (developers, release, devops, qa engineers). Ο στόχος είναι να μπορούν όλοι αυτοί να συννενοούνται χωρίς να πλακώνονται, σχετικά με το **τι θέλει ο πελάτης**.

Χρησιμοποιώντας την μέθοδο BDD, καταφέρνουμε να μειώσουμε το επικοινωνιακό χάος καθώς μειώνουμε αισθητά το feedback loop. Ακούγεται πολύ καλό, σωστά; Ίσως και να είναι, πάντως αρκετοί έχουν αρχίσει να το υιοθετούν σε *agile* ομάδες.

Η εφαρμοφή του BDD χρησιμοποιεί μία γλώσσα που λέγεται [Gherkin](https://cucumber.io/docs/gherkin/reference/), η οποία χρησιμοποιείται για την συγγραφή testing plans (αρχεία που τελειώνουν σε `*.feature`). Πρόκειται στην ουσία για απλά Αγγλικά σε συνδυασμό με κάποιες λέξεις κλειδιά ([Given, When, Then, And](https://martinfowler.com/bliki/GivenWhenThen.html)) τα οποία γίνονται *έξυπνα* parsing από τον κώδικα (δηλαδή το Godog framework που διαβάζει αυτά τα testing plans). Έτσι οι *άσχετοι με τον κώδικα* (αλλά σχετικοί με τις προδιαγραφές το προϊόντος) μπορούν και συνεννοούνται με τους developers -- και το αντίθετο.

> Διαβάστε περισσότερα για την [Gherkin](http://docs.behat.org/en/v2.5/guides/1.gherkin.html)

## Godog: Το Cucumber της Go

Πρόκειται για την επίσημη εκδοχή σε Go, του γνωστού open source BDD framework [Cucumber](https://cucumber.io/). Ένας από τους βασικούς προγραμματιστές του Cucumber φαίνεται πως είναι *gopher* στα κρυφά, συνεπώς το [godog](https://github.com/DATA-DOG/godog)
γεννήθηκε.

### Δεν παίζει με το go test

Συγκριτικά με άλλα testing framework, η godog δεν συνεργάζεται με το `go test`. Χρησιμοποιεί μία δική της διαφορετική φιλοσοφία, όπου χρησιμοποιεί τα δικά της αρχεία για την περιγραφή της συμπεριφοράς του προϊόντας (feature files) και συνεπώς χρειάζεται ξεχωριστό building (βλέπε `godog *.feature`). Με άλλα λόγια: το `go test` δεν λειτουργεί εδώ.

### Εγκατασταση της godog

Όπως και κάθε άλλη βιβλιοθήκη της Go, η εγκατάσταση είναι αυτό που λένε στην πιάτσα *go-gettable*, δηλαδή:

```golang
go get github.com/DATA-DOG/godog/cmd/godog
```

## Γράφοντας tests με την βοήθεια της Godog

Αφού εγκαταστήσετε την Godog, το επόμενο βήμα είναι να φτιάξουμε ένα αρχείο που να περιγράφει μία λειτουργία του προγραμματός μας. Αυτό το αρχείο ονομάζεται **feature file**. Μπορείτε να το ονομάσετε όπως θέλετε, αρκεί μόνο η κατάληξη να είναι `.feature`. Στην προκειμένη περίπτωση, πρόκειται να φτιάξουμε ένα πρόγραμμα που κάνει κάτι πολύ απλό: *μία πρόσθεση δύο ακεραίων αριθμών*. Ήρθε η ώρα λοιπόν για meeting με τον Product Owner, ώστε να μας εξηγήσει **τι** θέλει να φτιάξουμε, και στην συνέχεια θα συζητήσουμε το **πώς** θα το υλοποίήσουμε.

> Meeting ... κλασσικά κοιμούνται όλοι

Αφού έχουμε ολοκληρώσει τις συζητήσεις μας, ήρθε η στιγμή να πάρουμε όλο αυτό το feedback από τους πελάτες και να το διοχετεύσουμε σε ένα *feature file*, το οποίο θα το ονομάσουμε `adding.feature`:

```gherkin
Feature: Add 2 numbers
  In order to perform addition of 2 numbers
  As a user
  I need to be able to calculate the sum of them

  Scenario: Add 5 to 4
    Given you have number 5
    When you add 4 to this number
    Then the summary result should be 9

  Scenario: Add 3 to 11
    Given you have number 3
    When you add 11 to this number
    Then the summary result should be 14
```

Όπως βλέπετε, το αρχείο αυτό είναι γραμμένο στην γλώσσα **Gherkin**, ωστόσο παραμένει κατανοητό και προσιτό σε όλους όσους ξέρουν να ομιλούν την αγγλική διάλεκτο. Και τώρα λοιπόν, ήρθε η ώρα να κάνουμε parsing αυτό το αρχείο, με την βοήθεια της Godog. Τρέχουμε λοιπόν:

```bash
$ godog  adding.feature
```

Και μας επιστρέφει:

```
Feature: Add 2 numbers
  In order to perform addition of 2 numbers
  As a user
  I need to be able to calculate the sum of them

  Scenario: Add 5 to 4                  # adding.feature:6
    Given you have number 5
    When you add 4 to this number
    Then the summary result should be 9

  Scenario: Add 3 to 11                  # adding.feature:11
    Given you have number 3
    When you add 11 to this number
    Then the summary result should be 14

2 scenarios (2 undefined)
6 steps (6 undefined)
63.503µs

You can implement step definitions for undefined steps with these snippets:

func youHaveNumber(arg1 int) error {
        return godog.ErrPending
}

func youAddToThisNumber(arg1 int) error {
        return godog.ErrPending
}

func theSummaryResultShouldBe(arg1 int) error {
        return godog.ErrPending
}

func FeatureContext(s *godog.Suite) {
        s.Step(`^you have number (\d+)$`, youHaveNumber)
        s.Step(`^you add (\d+) to this number$`, youAddToThisNumber)
        s.Step(`^the summary result should be (\d+)$`, theSummaryResultShouldBe)
}
```

Έχουμε λοιπόν 2 σενάρια `undefined` το οποίο είναι απολύτως λογικό, αφού ακόμα δεν έχουμε γράψει καθόλου κώδικα: ούτε τον κώδικα της εφαρμογής μας, ούτε τον κώδικα που θα την τεστάρει. Πριν το κάνουμε αυτό, κάνουμε *copy paste* το *output* σε ενα καινοριο αρχείο με την ονομασία `adding_test.go`. Αυτό θα είναι και ο κώδικας για testing με την Godog. Όπως κάθε άλλο αρχείο, πρέπει να αρχίζει με `packaging main` και το *import* της godog:

```golang
package main

import "github.com/DATA-DOG/godog"

func youHaveNumber(arg1 int) error {
        return godog.ErrPending
}

func youAddToThisNumber(arg1 int) error {
        return godog.ErrPending
}

func theSummaryResultShouldBe(arg1 int) error {
        return godog.ErrPending
}

func FeatureContext(s *godog.Suite) {
        s.Step(`^you have number (\d+)$`, youHaveNumber)
        s.Step(`^you add (\d+) to this number$`, youAddToThisNumber)
        s.Step(`^the summary result should be (\d+)$`, theSummaryResultShouldBe)
}
```

Ας τρέξουμε ξανά

```bash
$ godog  adding.feature

Feature: Add 2 numbers
  In order to perform addition of 2 numbers
  As a user
  I need to be able to calculate the sum of them

  Scenario: Add 5 to 4                  # adding.feature:6
    Given you have number 5             # adding_test.go:6 -> youHaveNumber
      TODO: write pending definition
    When you add 4 to this number       # adding_test.go:10 -> youAddToThisNumber
    Then the summary result should be 9 # adding_test.go:14 -> theSummaryResultShouldBe

  Scenario: Add 3 to 11                  # adding.feature:11
    Given you have number 3              # adding_test.go:6 -> youHaveNumber
      TODO: write pending definition
    When you add 11 to this number       # adding_test.go:10 -> youAddToThisNumber
    Then the summary result should be 14 # adding_test.go:14 -> theSummaryResultShouldBe

2 scenarios (2 pending)
6 steps (2 pending, 4 skipped)
158.597µs
```

Πολύ καλύτερα! Πλέον έχουμε 2 pending scenarios και τα υπόλοιπα είναι skipped (αφού τα προηγούμενα δεν έχουν υλοποιηθεί, λογικό να τα προσπεράσει). Ας ανοίξουμε το `adding_test.go` και να κάνουμε refactor το test μας. Για να δούμε τι γίνεται εκεί...

Η πιο σημαντική συνάρτηση είναι η `FeatureContext(s *godog.Suite)` η οποία κάνει το *parsing* και είναι αρκετά έξυπνη ώστε να περνάει τις τιμές (νούμερα) που δώσαμε νωρίτερα στο `adding.feature` στις αντίστοιχες συναρτήσεις:

```golang
func FeatureContext(s *godog.Suite) {
	s.Step(`^you have number (\d+)$`, youHaveNumber)
	s.Step(`^you add (\d+) to this number$`, youAddToThisNumber)
	s.Step(`^the summary result should be (\d+)$`, theSummaryResultShouldBe)
}
```

Για παράδειγμα, με βάση το *Σενάριο 1* έχουμε:
```
youHaveNumber # 5
youAddToThisNumber # 4
theSummaryResultShouldBe # 9
```

Το οποίο έχει σαν συνέπεια να περνάει αυτές τις τιμές στις παραπάνω συναρτήσεις:

```golang
func youHaveNumber(arg1 int) error            // όπου arg1 == 5
func youAddToThisNumber(arg1 int) error       // όπου arg1 == 4
func theSummaryResultShouldBe(arg1 int) error // όπου arg1 == 9
```

Ας φτιάξουμε λοιπόν 3 μεταβλητές για να τις μοιραζόμαστε μαζί με τις άλλες συναρτήσεις:

```golang
var testX, testY, testResult int
```

Ας πούμε λοιπόν ότι η `testX` είναι `5`. Για να γίνει αυτό, δεν έχω να τεστάρω κάτι:

```golang
func youHaveNumber(arg1 int) error {
	testX = arg1
	return nil
}
```

Το ίδιο ισχύει και για το `testY` που θα το βάλω να είναι `4`. 

## Developing του feature χρησιμοποιώντας TDD

Και ήρθε η στιγμή να κάνω την πρόσθεση. Θα **υποθέσω** ότι έχω γράψει το αρχικό μου πρόγραμμα, στο οποίο θα έχω **υποθετικά** μια συνάρτηση που κάνει την πρόσθεση (ας την ονομάσω **υποθετικά** `AddTwoNumbers(x, y int)`) και επιστρέφει **υποθετικά** το άθροισμα των δύο αριθμών (`return x+y`).

```golang
func youAddToThisNumber(arg1 int) error {
	testY = arg1
	testResult = AddTwoNumbers(testX, testY)
	return nil
}
```

Προφανώς δεν υπάρχει (όχι ακόμα!) τέτοια συνάρτηση (να περιμένετε ότι ο *go-linter* θα διαμαρτυθεί περί `undefined` συναρτήσεως). Αυτή είναι όλη η λογική του [TDD (Test Driven Development)](https://en.wikipedia.org/wiki/Test-driven_development) όπου πρώτα γράφουμε το test για ένα feature, μετά τρέχουμε το τεστ (**ξέρουμε εκ των προτέρων ότι θα αποτύχει**), και στην συνέχεια προσπαθούμε να κάνουμε το test να πετύχει. Αυτό θα γίνει μόνο όταν θα έχουμε ολοκληρώσει την συγγραφή του feature μας. Πάμε λοιπόν να γράψουμε τον βασικό κώδικα του feature της πρόσθεσης που μας ζητήθηκε. Ανοίγουμε ένα αρχείο: `main.go` και γράφουμε:

```golang
package main

import "fmt"

// Add two numbers
var x = 2
var y = 4
var result int

// AddTwoNumbers x + y
func AddTwoNumbers(x, y int) (result int) {
	result = x + y
	return result
}

func main() {
	result := AddTwoNumbers(x, y)
	fmt.Printf("The result is %d\n", result)
}
```

Όπως βλέπετε, έχω αρχικοποιήσει 2 μεταβλητές (`x,y`) ώστε να επιστρέφει ένα *default* αποτέλεσμα, για να μην έχω θέματα με το *user input*. Άλλωστε δεν πρόκειται για κάτι ολοκληρομένο, αλλά για ένα απλό παράδειγμα ώστε να καταλάβετε πως δουκλεύει το BDD και η Godog:

```bash
$ go run main.go 
The result is 6
```

## Refactoring του test

Οπότε τώρα μπορούμε να επιστρέψουμε πίσω στο test μας (`adding_test.go`) και να συνεχίσουμε με το τρίτο μέρος, όπου ελέγχουμε αν η συνάρτηση `AddTwoNumbers` θα επιστρέψει το αποτέλεσμα που περιμένουμε με βάση το `adding.feature`:

```golang
func theSummaryResultShouldBe(arg1 int) error {
	if testResult != arg1 {
		return fmt.Errorf("There's an error. %d + %d returned %d instead of %d", testX, testY, testResult, arg1)

	}
	return nil
}
```

Και κάπως έτσι έχουμε ολόκληρώσει το testing μας. Για να το τρέξουμε:

```bash
$ godog adding.feature
```

Το οποίο μας επιστρέφει:

```bash
Feature: Add 2 numbers
  In order to perform addition of 2 numbers
  As a user
  I need to be able to AddTwoNumbers the sum of them

  Scenario: Add 5 to 4                  # adding.feature:6
    Given you have number 5             # adding_test.go:13 -> youHaveNumber
    When you add 4 to this number       # adding_test.go:19 -> youAddToThisNumber
    Then the summary result should be 9 # adding_test.go:25 -> theSummaryResultShouldBe

  Scenario: Add 3 to 11                  # adding.feature:11
    Given you have number 3              # adding_test.go:13 -> youHaveNumber
    When you add 11 to this number       # adding_test.go:19 -> youAddToThisNumber
    Then the summary result should be 14 # adding_test.go:25 -> theSummaryResultShouldBe

2 scenarios (2 passed)
6 steps (6 passed)
160.576µs
```

Σαν αποτέλεσμα, αν αποτύχει κάποιο από αυτά τα tests, είναι πολύ εύκολο για κάποιον που δεν έχει ιδέα από τον κώδικα, να καταλάβει τι απέτυχε σε business επίπεδο, έτσι ώστε να αξιολογήσει την σημαντικότητα αυτού. Επιτρέψτε μου να αλλάξω επίτηδες το αρχείο έτσι ώστε να δείξω πως φαίνεται ένα σφάλμα. Ας πούμε (ψέμματα) ότι *3+12=14*:

```gherkin
Scenario: Add 3 to 11
    Given you have number 3
    When you add 12 to this number
    Then the summary result should be 14
```

Κι ας τρέξουμε ξανά το `godog planning.feature`:

```bash
Feature: Add 2 numbers
  In order to perform addition of 2 numbers
  As a user
  I need to be able to AddTwoNumbers the sum of them

  Scenario: Add 5 to 4                  # adding.feature:6
    Given you have number 5             # adding_test.go:13 -> youHaveNumber
    When you add 4 to this number       # adding_test.go:19 -> youAddToThisNumber
    Then the summary result should be 9 # adding_test.go:25 -> theSummaryResultShouldBe

  Scenario: Add 3 to 11                  # adding.feature:11
    Given you have number 3              # adding_test.go:13 -> youHaveNumber
    When you add 12 to this number       # adding_test.go:19 -> youAddToThisNumber
    Then the summary result should be 14 # adding_test.go:25 -> theSummaryResultShouldBe
    There's an error. 3 + 12 returned 15 instead of 14

--- Failed steps:

  Scenario: Add 3 to 11 # adding.feature:11
    Then the summary result should be 14 # adding.feature:14
      Error: There's an error. 3 + 12 returned 15 instead of 14


2 scenarios (1 passed, 1 failed)
6 steps (5 passed, 1 failed)
164.339µs
```

Δείτε πόσο όμορφο σφάλμα έχουμε:

> Error: There's an error. 3 + 12 returned 15 instead of 14

Στην περίπτωση που δεν χρησιμοποιήσουμε BDD αλλά απλό unit testing με το πακέτο `testing` της Go, το test θα έμοιαζε κάπως έτσι (ανοίξτε αρχείο `main_test.go`):

```golang
package main

import "testing"

func TestAddTwoNumbers(t *testing.T) {
	if AddTwoNumbers(12, 3) != 15 {
		t.Error("Expected 12 + 3 to equal 15")
	}
}
```

Και τρέξτε `go test`:

```bash
PASS
ok      github.com/drpaneas/write_a_test        0.006s
```

Χμ, μάλιστα. Άντε να βγάλει άκρη τώρα κανείς από αυτό... *τι* έτρεξε και *τι* έκανε. Η κλασσική απάντηση των προγραμματιστών είναι *δες τον κώδικα* και κάπου εκεί είναι το σημείο που τελειώνουν όλα και δεν επικοινωνεί κανείς με κανέναν. Στην προκειμένη περίπτωση του παραδείγματος, ο κώδικας είναι υπερβολικά εύκολος και δεν θα ήταν πρόβλημα για κάποιον να τον διαβάσει και να καταλάβει τι τεστάρεται, αλλά σκεφτείτε πολύ πιο σύνθετα σενάρια.

> jfyi o κώδικας του όλου project βρίσκεται [εδώ](https://github.com/drpaneas/write_a_test)

Τι πιστεύτε λοιπόν; Αξίζει το BDD;
