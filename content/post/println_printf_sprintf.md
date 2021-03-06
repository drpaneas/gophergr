+++

categories = ["functions"]
date = "2019-06-13"
tags = ["fmt", "printf", "sprintf", "println"]
slug = "diamorfonontas-to-output-me-Println-Printf-Sprintf"
title = "Διαμορφώνοντας το output με Println, Printf και Sprintf"

+++

Από τις πρώτες ασκήσεις προγραμματισμού που μαθαίνει κανείς είναι
αυτές που σχετίζονται με την _εκτύπωση_ διαφόρων δεδομένων στο
τερματικό. Η πιο _"κλασσική"_ συνάρτηση που υπάρχει σε όλες τις 
γλώσσες προγραμματισμού είναι η **print**. Αυτό θα είναι το αντικείμενο
του άρθρου σήμερα; να μάθουμε πώς να κάνουμε `print` διαφόρους
τύπους δεδομένων στην _golang_ και να εξικοιωθούμε με τρεις
από τις διάφορες παραλλαγές αυτής (`Println`, `Printf` και `Sprintf`).
Προφανώς και υπάρχουν κι άλλες, αλλά αυτές οι τρεις χρησιμοποιούνται
πιο συχνά. Πάμε λοιπόν! Ας δούμε το παρακάτω κομμάτι κώδικα:

```golang
// Ορισμοί πακέτων κλπ παραλείπονται για ευνόητους λόγους
func main() {
    var x, y, area float64
    // Υπολόγισε το εμβαδό
    x = 4.2
    y = 3.0
    area = y * x
    fmt.Println(area, "τετραγωνικά μέτρα")
}
```

> Κάνε κλικ [εδώ](https://play.golang.org/p/mzHXrmaqwer) για να τρέξεις τον κώδικα.

Η έξοδος είναι:

> 12.600000000000001 τετραγωνικά μέτρα

Αν και το παραπάνω πρόγραμμα δουλεύει, αυτό δεν σημαίνει ότι δεν έχει προβλήματα:

* Η στρογγυλοποίηση γίνεται με έναν παράξενο τρόπο και για να πούμε την αλήθεια δεν μας ενδιαφέρει ιδιαίτερα να δω τόσα πολλά δεκαδικά ψηφία.
* [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). Μοιάζει με κομμάτι του κώδικα που θα ήταν καλύτερο να γραφτεί μία συνάρτηση για αυτό, ώστε αν χρειαστώ τον υπολογισμό αυτό αργότερα, να μην κάνουμε duplicate τον κώδικα.

Σε αυτό το άρθρο θα μιλήσω για το πρώτο μέρος του προβλήματος, καθώς έχω σκοπό να γράψω για συναρτήσεις σε άλλο άρθρο. Η λύση που βρήκα είναι απλή και μπακάλικη: Στρογγυλοποίηση σε δύο δεκαδικά. Προσωπικά μου αρκούν - και νομίζω για τον περισσότερο κόσμο είναι ΟΚ.

## Printf

Το πακέτο [fmt](https://godoc.org/fmt) διαθέτει μία συνάρτηση που λέγεται [Printf](https://godoc.org/fmt#Printf) η οποία σημαίνει κάτι όπως **print** with **formatting**.

```golang
// Printf formats according to a format specifier and writes to standard output.
// It returns the number of bytes written and any write error encountered.
func Printf(format string, a ...interface{}) (n int, err error) {
	return Fprintf(os.Stdout, format, a...)
}
```

Συνεπώς το παραπάνω παράδειγμα μπορεί να διορθώθεί με:

```golang
fmt.Printf("%0.2f τετραγωνικά μέτρα\n", area)
```

Έξοδος:

```golang
12.60 τετραγωνικά μέτρα
```

## Sprintf

Η [Sprintf](https://sourcegraph.com/github.com/golang/exp/-/blob/errors/fmt/print.go#L189-196) είναι μία ακόμα συνάρτηση του [fmt](https://godoc.org/fmt) package, η οποία λειτουργεί όπως και η `Printf`, με την διαφορά ότι __επιστρέφει__ το `string` χωρίς ντε-και-καλά να το κάνει __printing__ στην έξοδο. Για αυτό προφανώς χρησιμοποιείται όταν θέλει κανείς να αποθηκεύσει ένα φορμαρισμένο string.

```golang
// Sprintf formats according to a format specifier and returns the resulting string.
func Sprintf(format string, a ...interface{}) string {
	p := newPrinter()
	p.doPrintf(format, a)
	s := string(p.buf)
	p.free()
	return s
}
```

Συνεπώς το παραπάνω παράδειγμα μπορεί να διορθωθεί με:

```golang
resultStr := fmt.Sprintf("%0.2f τετραγωνικά μέτρα\n", area)
fmt.Printf(resultStr)
```

Έξοδος:

```golang
12.60 τετραγωνικά μέτρα
```

Από ότι φαίνεται και οι δύο συναρτήσεις, `fmt.Printf()` και `fmt.Sprintf()` είναι
ικανές να διορθώσουν το πρόβλημα που είχα, μειώνοντας τα δεκαδικά ψηφία σε μόνο δύο.
Στην περίπτωση που σας φαίνεται περίεργη η υλοποίησή τους, τότε ίσως δεν ξέρετε
να χρησιμοποιείτε τα __formatting verbs__. Για παράδειγμα, όλες αυτές οι `Printότιναναι`
συναρτήσεις, συνήθως έχουν μία σύνταξη όπως το παράδειγμά μας: `%0.2f`. Σε αυτό
το παράδειγμα:

* Το `%0.2f` είναι formatting verb
* Και το `0.2` είναι το value αυτου

Μερικά άλλα παραδείγματα:

```golang
// verbs: %s και %d
// values: "ΠΑΟΚ" και 2009
fmt.Printf("Ο %s πήρε πρωτάθλημα το %d\n", "ΠΑΟΚ", 2009)

// verbs: %f
// values: 12.600000
fmt.Printf("%f τετραγωνικά μέτρα\n", 4.2*3.0)
```

Έξοδος:

```
Ο ΠΑΟΚ πήρε πρωτάθλημα το 2009
12.600000 τετραγωνικά μέτρα
```

Διαβάζοντας το documentation του fmt package, μερικά από τα πιο συνήθες verbs
είναι τα εξής:

```golang
func main() {
	fmt.Printf("Δεκαδικός: %f\n", 3.1415)
	fmt.Printf("Ακέραιος: %d\n", 13)
	fmt.Printf("String: %s\n", "Μα ποιος λέει αλφαριμητικό;")
	fmt.Printf("Boolean: %t\n", false)
	fmt.Printf("Τύπος: %T %T %T %T\n", 3.1415, 13, "pizza", true)
	fmt.Printf("Τιμές: %v %v %v\n", 1.13, "\t", true)
	fmt.Printf("Τιμές: %#v %#v %#v\n", 1.13, "\t", true)
}
```

> Τρέξτε τον κώδικα κάνοντας κλικ [εδώ](https://play.golang.org/p/DpdIRCPlRiC)

Έξοδος:

```
Δεκαδικός: 3.141500
Ακέραιος: 13
String: Μα ποιος λέει αλφαριμητικό;
Boolean: false
Τύπος: float64 int string bool
Τιμές: 1.13 	 true
Τιμές: 1.13 "\t" true
```

Aπό αυτά αξίζει να σημειώσετε την χρήση του `%#v` η οποία στην ουσία
επιστρέφει αυτούσια την τιμή έτσι όπως γράφεται στον κώδικα της Go.
Κάτι τέτοιο είναι χρήσιμο σε περίπτώσεις _debugging_.

## Δεκαδικά και character padding

Κλείνοντας, θέλω να μιλήσω για την λύση που δώσαμε νωρίτερα χρησιμοποιώντας
το `%0.2f` verb, καταφέρνοντας έτσι να περιορίσουμε τα δεκαδικά ψηφία σε
μόνο 2. Η ίδια λογική μπορεί να εφαρμοστεί και για `strings` και ιδιαίτερα
σε περιπτώσεις όπου θέλουμε να έχουμε ομοιόμορφη στοίχιση και άλλα περίεργα
_nerd_-ουλίστικα θέματα που έχουν να κάνουν με την έξοδο ενός προγράμματος
στο τερματικό. Για παράδειγμα:

```golang
fmt.Printf("%30s | %s\n", "Προσθήκη στο καλάθι", "Τιμή")
fmt.Printf("- - - - - - - - - - - - - - - - - - - - - -\n")
fmt.Printf("%30s | %0.2f EUR\n", "Πίτσα Σπέσιαλ με έξτρα κασέρι", 7.80)
fmt.Printf("%30s | %0.2f EUR\n", "Πίτα γύρο με πατάτες", 3.5)
fmt.Printf("%30s | %0.2f EUR\n", "Φαλάφελ για Vegan", 4.50)
```

> Τρέξτε τον κώδικα [εδώ](https://play.golang.org/p/OWpi8XKk2ZK)

Έξοδος:

```
	   Προσθήκη στο καλάθι | Τιμή
- - - - - - - - - - - - - - - - - - - - - -
 Πίτσα Σπέσιαλ με έξτρα κασέρι | 7.80 EUR
          Πίτα γύρο με πατάτες | 3.50 EUR
             Φαλάφελ για Vegan | 4.50 EUR
```

Όπως βλέπετε μπορούμε να χρησιμοποιήσουμε αυτό το _τρικ_ για να κάνουμε
padding. Καλή φάση!