+++

categories = ["libraries"]
date = "2019-07-28"
tags = ["sdl", "graphics", "games"]
slug = "Ksekinontaw-me-tin-SDL2"
title = "Παίζοντας με την SDL2"

+++

# Τι είναι η SDL

Όσοι ασχολείστε με game development (και όσοι θέλετε να ασχοληθείτε) ίσως έχετε ακούσει για την **SDL**. Πολλοί νομίζουν ότι είναι ένα *game framework* ή μία *game engine* αλλά στην πραγματικότητα δεν είναι *κανένα* από τα δύο αυτά πράγματα. Τι είναι λοιπόν; Μιλώντας αποκλειστικά με τεχνικούς όρους, η SDL είναι ένα **OS abstraction layer**. Βλέπετε, για να φτιάξεικανείς ένα *videogame* πρέπει να μπορεί να μιλήσει με το λειτουργικό σύστημα και να σιγουρευτεί ότι μπορεί να πραγματοποιήσει κάποια βασικά πράγματα, όπως:

* Εμφάνιση διαφόρων εικόνων (pixels) στην οθόνη
* Αναπαραγωγή ήχου
* Είσοδο από το τηλεχειριστήριο ή ποντίκι/πληκτρολόγιο ή οθόνη αφής
* Αποθήκευση προόδου και φόρτωση αυτής (πχ savegame)
* Δίκτυο

Για όλα τα παραπάνω πράγματα, πρέπει ο προγραμματιστής να *μιλήσει* με το hardware. Να επικοινωνήσει με την οθόνη, το πληκτρολόγιο, τα ηχεία, τον σκληρό δίσκο, την κάρτα δικτύου κλπ. Η επικοινωνία αυτή γίνεται μέσω του λειτουργικού συστήματος, και κάθε OS (Operating System) υλοποιεί τα παραπάνω με διαφορετικό τρόπο. Οπότε αν θέλετε να φτιάξετε ένα παιχνίδι που να τρέχει σε παραπάνω από ένα λειτουργικά συστήματα τότε πρέπει να _γράψετε_ όλα τα παραπάνω επικοινωνιακά μέρη, παραπάνω από μία φορά. Για παράδειγμα αν θέλετε να φτιάξετε ένα παιχνίδι για 3 πλατφόρμες (Linux, Mac, Windows), τότε *ουστιστικά* πρέπει να ξαναγράψετε 3 φορές το ίδιο παιχνίδι. Μερικές γλώσσες προγραμματισμού περιέχουν βιβλιοθήκες που λειτουργούν ανεξαρτήτως λειτουργικού συστήματος. Για παράδειγμα, αν θέλετε να διαβάσετε ένα αρχείο στην Go, δεν χρειάζεται να καλέσετε το Microsoft Windows API ή το Linux API. Οι core libraries της Go έχουν built-in function που θα βγάλουν εις πέρας την λειτουργία ανεξαρτήτως λειτουργικού. Αλλά δεν σημαινει όμως ότι το να ζωγραφίζεις pixels ή να αναπαράγεις ήχους είναι κομμάτι αυτών των
core libraries. Γιατί; Δεν ξέρω, ίσως είναι αρκετή δουλειά και είναι τεμπέλιδες. Εδώ έρχεται λοιπόν η **SDL 2**, η οποία είναι μία βιβλιόθηκη που κάνει όλα τα παραπάνω και έχει σχεδιαστεί με σκοπό να βοηθήσει game programmers. Η SDL έχει Golang bindings, που σημαίνει ότι μπορείτε να την κάνετε απλά `import` και να την χρησιμοποιήσετε. Έτσι, αντί να γράφετε OS API functions, θα καλείτε απλά SDL2 functions για να πραγματοποίησετε την επικοινωνία με το hardware ανεξαρτήτως λειτουργικού συστήματος. Σαν αποτέλεσμα, θα γράψετε τον κώδικα **μία φορά** και αυτός θα παίζει σε όλα τα λειτουργικά συστήματα που υποστηρίζονται από την SDL.

# Πώς την κατεβάζω

Αρχικά πηγαίνετε στο επίσημο [download page](https://www.libsdl.org/download-2.0.php) και επιλέξετε τα Development Libraries. Για όσους έχουν Windows, θα χρειαστούν το [SDL2-devel-2.0.10-mingw.tar.gz](https://www.libsdl.org/release/SDL2-devel-2.0.10-mingw.tar.gz). Για τους λινουξάδες τις υπόθεσης, μπορούν να χρησιμοποιήσουν τον package manager της διανομής τους, Ψάξτε για `libsdl2`:

```bash
$ sudo apt-cache search libsdl2
```

Κατεβάστε τα _development files_ για την sdl2:

```bash
sudo apt-get install libsdl2-dev
```

Το πακέτο αυτό θα εγκαταστήσει ένα binary (`/usr/bin/sdl2-config`), μία σειρά από *C Headers* στο directory
`/usr/include/SDL2/` και την βιβλιοθήκη (καθώς επίσης και το symlink αυτής) `/usr/lib/x86_64-linux-gnu/libSDL2.so -> libSDL2-2.0.so.0.4.1`. Όπως είναι γνωστό σχεδόν κάθε γλώσσα έχει τον τρόπο της να επικοινωνεί με βιβλιοθήκες
γραμμένες σε C. Εστσι λοιπόν και η Go, διαθέτει τρόπο να μιλήσει με την C Library της SDL2. Κάποιοι καλοί άνθρωποι λοιπόν που λέγονται [Veandco](https://github.com/veandco/go-sdl2) έχουν φτιάξει τα απαραίτητα Go-bindings για την SDL τα οποία και θα χρησιμοποιήσουμε. Μάλιστα, διαθέτουν αρκετά ενημερωμένο documentation! Στην περίπτωση που δεν βρήκατε πώς να εγκαταστήσετε νωρίτερα την SDL, ρίχτε μια ματιά στα [Requirements](https://github.com/veandco/go-sdl2#documentation) των Veadco. Στην περίπτωσή μου (debian) έδωσα επιπλέον την εξής εντολής:

```bash
sudo apt install libsdl2{,-image,-mixer,-ttf,-gfx}-dev
```

Για την εγκατάσταση των bindings μπορουμε να χρησιμοποιήσουμε το εργαλείο `go -get` ως εξής:

```go
$ go get -v github.com/veandco/go-sdl2/{sdl,img,mix,ttf}

# Output
github.com/veandco/go-sdl2 (download)
github.com/veandco/go-sdl2/sdl
github.com/veandco/go-sdl2/img
github.com/veandco/go-sdl2/ttf
github.com/veandco/go-sdl2/mix
```

## Προγραμματίζοντας με την SDL

### Σέταρε ένα παράθυρο

Τώρα πλέον που έχουμε σετάρει την SDL, ήρθε η στιγμή να φτιάξουμε το πιο απλό πρόγραμμα χρησιμοποιώντας της. Κάτι σαν το αντίστοιχο _hello world_. Αυτό που θα κάνουμε σε πρώτη φάση είναι να φτιάξουμε ένα παράθυρο (Window). Κάθε παιχνίδι/εφαρμογή χρειάζεται να τρέχει μέσα στο δικό της παράθυρο, συνεπώς ας ξεκινήσουμε από αυτό.

H function που φτιάχνει το παράθυρο λέγεται [CreateWindow](https://wiki.libsdl.org/SDL_CreateWindow) και μοιάζει κάπως έτσι:

```go
// CreateWindow creates a window with the specified position, dimensions, and flags.
// (https://wiki.libsdl.org/SDL_CreateWindow)
func CreateWindow(title string, x, y, w, h int32, flags uint32) (*Window, error) {
	var _window = C.SDL_CreateWindow(C.CString(title), C.int(x), C.int(y), C.int(w), C.int(h), C.Uint32(flags))
	if _window == nil {
		return nil, GetError()
	}
	return (*Window)(unsafe.Pointer(_window)), nil
}
```

Η `sdl.CreateWindow()` επιστρέφει το παράθυρο `*Window` και ένα `error`. Συνεπώς, όπως σε κάθε Go πρόγραμμα, μετά την κλήση της συναρτησης θα έχουμε το κλασσικό `if err != nil` snippet κώδικα. Πάμε να δούμε τα ορίσματά της:

* title: Ο τίτλος του παραθύρου σε κωδικοποίηση UTF-8
* x: Η θέση x του παραθύρου στην οθόνη. Συχνές επιλογές: `SDL_WINDOWPOS_CENTERED` ή `SDL_WINDOWPOS_UNDEFINED`
* y: Η θέση y του παραθύρου στην οθόνη. Συχνές επιλογές: `SDL_WINDOWPOS_CENTERED` ή `SDL_WINDOWPOS_UNDEFINED`
* w: Το μήκος του παραθύρου, σε pixels (πχ 1920) για FullHD
* h: το ύψος του παραθύρου, σε pixels (πχ 1080) για FullHD
* flags: Είτε 0 είτε μία από [αυτές](https://wiki.libsdl.org/SDL_WindowFlags)

> Η flag που θα χρησιμοποιήσουμε είναι η `SDL_WINDOW_SHOWN` η οποία κάνει το παράθυρο visible στην οθόνη.

Άλλες κλασσικές επιλογές για flags είναι οι:

* SDL_WINDOW_FULLSCREEN:    Κάνει το παράθυρο fullscreen (κλασσική μαύρη οθόνη) στο τρέχων desktop
* SDL_WINDOW_OPENGL:    Φτιάχνει παράθυρο χρησιμοποιώντας OpenGL
* SDL_WINDOW_VULKAN: Φτιάχνει παράθυρο χρησιμοποιώντας το νέο Vulkan API

Συνεπώς ας γράψουμε τον παρακάτω κώδικα:

```go
package main

import (
    "fmt"

    "github.com/veandco/go-sdl2/sdl"
)

func main() {
    window, err := sdl.CreateWindow("Testing SDL2", sdl.WINDOWPOS_UNDEFINED, sdl.WINDOWPOS_UNDEFINED, 800, 600, sdl.WINDOW_SHOWN)
    if err != nil {
        errMsg := fmt.Errorf("\n[ERROR]:\tWindow Creation failed.\n[E_LOG]:\t%s", err)
        fmt.Println(errMsg)
        return
    }

    sdl.Delay(2000) // Περίμενε για 2 seconds πριν κλείσεις
}
```

Ο παραπάνω κώδικας θα εμφανίσει (χάρις της `SDL_WINDOW_SHOWN` flag) ένα παράθυρο στην οθόνη, σε τυχαία θέση, ανάλυσης _800x600_. Στην περίπτωση που αποτύχει, τότε θα δούμε ένα όμορφο σφάλμα. Αυξήστε επίτηδες την ανάλυση και δοκιμάστε ξανά:

```go
$ go run sdl2.go 

[ERROR]:        Window Creation failed.
[E_LOG]:        Window is too large.
```

#### Διέγραψε το παράθυρο

Μία καλή πρακτική στον προγραμματισμό γενικότερα, είναι να καταστρέφουμε τα pointer objects (πχ το `*Window`) όταν δεν τα χρειαζόμαστε. Αν και στο συγκεκριμένο παράδειγμα είναι αχρείαστο να το κάνουμε (με την ολοκλήρωση της `main()` θα διεγραφούν όλα έτσι κι αλλιώς) θα ήταν καλό να το πράττουμε από συνήθεια. Στην προκειμένη περίπτωση, θα χρησιμοποιηούσαμε την ιδιότητα `Destroy()` του `*Window`, δηλαδή: 

```go
window, err := sdl.CreateWindow("Testing SDL2", sdl.WINDOWPOS_UNDEFINED, sdl.WINDOWPOS_UNDEFINED, 800, 600, sdl.WINDOW_SHOWN)
if err != nil {
    return err
}
sdl.Delay(2000)
window.Destroy()
```

Ένας καλός gopher, θα χρησιμοποιούσε την `defer` για να σιγουρευτεί ότι η `Destroy()` θα καλεστεί σίγουρα μετά την ολοκλήρωση της συνάρτησης. Τι εννοούμε με αυτό; Σκεφτείτε ότι γράφετε ένα παιχνίδι, και το τελευταίο που έχετε να κάνετε είναι να κλείσετε το παράθυρο, συνεπώς δεν θα γράψετε `window.Destroy()` παρά μόνον όταν θα έχετε τελειώσει την συγγραφή του παιχνιδιού σας. Για να μην το ξεχάσετε λοιπόν, είναι καλό να την χρησιμοποιήσετε αμέσως μετά το σημείο που την δημιουργείτε:

```go
package main

import (
	"fmt"

	"github.com/veandco/go-sdl2/sdl"
)

func main() {
	window, err := sdl.CreateWindow("Testing SDL2", sdl.WINDOWPOS_UNDEFINED, sdl.WINDOWPOS_UNDEFINED, 1232920, 1080, sdl.WINDOW_SHOWN)
	if err != nil {
		errMsg := fmt.Errorf("\n[ERROR]:\tWindow Creation failed.\n[E_LOG]:\t%s", err)
		fmt.Println(errMsg)
		return
	}
    defer window.Destroy() // Ας την ετοιμασω τώρα που ξέρω τι μου γίνεται
    sdl.Delay(2000)
    // Κώδικας
    // Περισσότερος κώδικας
    // Χάος από κώδικα

	// Σιγά μην θυμώμουν να την βάλω σε αυτό το σημείο
}
```

### Ζωγραφίστε μέσα στο παράθυρο

#### Αρχικά θέλουμε έναν 2D Renderer

Κάπου εδώ λοιπόν ξεκινάει το ταξίδι του _graphics programming_. Καταρχάς, ένα από τα πρώτα πράγματα που θα χρειαστεί να σετάρουμε προκειμένου να μπορέσουμε να σχεδιάσουμε/ζωγραφίσουμε μέσα στο `window` που φτιάξαμε νωρίτερα, είναι ένας **renderer**. Για να το κάνουμε αυτό χρησιμοποιούμε μία αντίστοιχη συνάρτηση με πριν, που λέγεται [CreateRenderer](https://wiki.libsdl.org/SDL_CreateRenderer) και μοιάζει κάπως έτσι:

```go
// CreateRenderer returns a new 2D rendering context for a window.
// (https://wiki.libsdl.org/SDL_CreateRenderer)
func CreateRenderer(window *Window, index int, flags uint32) (*Renderer, error) {
	renderer := C.SDL_CreateRenderer(window.cptr(), C.int(index), C.Uint32(flags))
	if renderer == nil {
		return nil, GetError()
	}
	return (*Renderer)(unsafe.Pointer(renderer)), nil
}
```

Η `sdl.CreateRenderer()` επιστρέφει τον renderer `*Renderer` και ένα `error`. Συνεπώς, όπως σε κάθε Go πρόγραμμα, μετά την κλήση της συναρτησης θα έχουμε το κλασσικό `if err != nil` snippet κώδικα. Πάμε να δούμε τα ορίσματά της:

* window: Ο συσχετισμός του renderer με ένα παράθυρο τύπου `*sdl.Window`. Εδώ θα βάλουμε το window που μόλις φτιάξαμε
* index: Διάλεξε τον rendering driver ή επέλεξε -1 για να αρχικοποιηθεί ο πρώτος συμβατός με τις flags
* flags: 0 είτε διάλεξε μία από [αυτες](https://wiki.libsdl.org/SDL_RendererFlags)

Συνδυάζοντας ότι μάθαμε προηγουμένως για την `sdl.CreateWindow`, έτσι και εδώ, η `sdl.CreateRenderer` πρέπει και αυτή να διαγραφτεί με την σειρά της. Σαν καλοί _gophers_ θα χρησιμοποιήσουμε και εδώ την `defer`. O τελικός κώδικας θα μοιάζει κάπως έτσι:

```go
renderer, err := sdl.CreateRenderer(window, -1, sdl.RENDERER_ACCELERATED)
if err != nil {
	errMsg := fmt.Errorf("\n[ERROR]:\tRenderer Creation failed.\n[E_LOG]:\t%s", err)
    fmt.Println(errMsg)
    return
}
defer renderer.Destroy()

sdl.Delay(1000) // Wait for 2 seconds
}
```

Εκτελώντας το πρόγραμμα, δεν θα δείτε καμία διαφορά σε σχέση με πριν, αφού δεν έχουμε σχεδιάσει κάτι ακόμα. Σημειώστε επίσης ότι χρησιμοποιήσαμε την _flag_ `RENDERER_ACCELERATED` η οποία σημαίνει ότι θα χρησιμοποιήσει την κάρτα γραφικών σας για να φτιάξει τον renderer. Στην περίπτωση που _δεν έχετε_ κάρτα γραφικών, τότε μπορείτε να χρησιμοποιήσετε την _flag_  `sdl.RENDERER_SOFTWARE`. Μπορούμε να χρησιμοποιήσουμε μία λάθος τιμή για τον driver του renderer (πχ `3`) έτσι ώστε να προκαλέσουμε _error_ ώστε να δούμε πως φαίνεται το μύνημα λάθους μας:

```go
$ go run sdl2.go 

[ERROR]:        Renderer Creation failed.
[E_LOG]:        index must be -1 or in the range of 0 - 2
```

#### Κάνε render ένα texture

Έχοντας πλέον σετάρει τον hardware accelerated 2D renderer, μπορούμε να κάνουμε render διάφορα πράγματα στο παράθυρό μας. Θα ξεκινήσουμε κάνοντας render ένα texture. Για αυτό θα χρησιμοποιήσουμε την συνάρτηση [CreateTexture](https://wiki.libsdl.org/SDL_CreateTexture) του render που μόλις φτιάξαμε που μοιάζει κάπως έτσι:

```go
// CreateTexture returns a new texture for a rendering context.
// (https://wiki.libsdl.org/SDL_CreateTexture)
func (renderer *Renderer) CreateTexture(format uint32, access int, w, h int32) (*Texture, error) {
	texture := C.SDL_CreateTexture(
		renderer.cptr(),
		C.Uint32(format),
		C.int(access),
		C.int(w),
		C.int(h))
	if texture == nil {
		return nil, GetError()
	}
	return (*Texture)(unsafe.Pointer(texture)), nil
}
```

Όπως και οι προηγούμενες συναρτήσεις, έτσι και αυτή, επιστρέφει 2 πράγματα: ένα `*Texture` και ένα `error`. Συνεπώς, όπως σε κάθε Go πρόγραμμα, μετά την κλήση της συναρτησης θα έχουμε το κλασσικό `if err != nil` snippet κώδικα. Πάμε να δούμε τα ορίσματά της:

* renderer: o renderer που φτιάξαμε πριν
* pixelformat: ένα array of bytes στην μνήμη, το πιο κλασσικό είναι το RGBA8888, όπου για κάθε χρώμα θα έχεις 1 byte. Δηλαδή 256 τιμές (2^8) για κάθε χρώμα. Στον υπολογιστή θα είναι κάτι σαν [255,0,0,0], όπου το 'A' είναι το transparency.
* Access: Δηλώνουμε πώς θα δουλευει το texture (θα αλλάζει συχνά; σπάνια; καθόλου; θα είναι locked;). [Διάλεξε](https://wiki.libsdl.org/SDL_TextureAccess)
* w: Το μήκος της texture
* h: Το ύψος της texture

Ο κώδικας λοιπόν διαμορφώνεται ως εξής:

```go
tex, err := renderer.CreateTexture(sdl.PIXELFORMAT_ABGR8888, sdl.TEXTUREACCESS_STREAMING, 1920, 1080)
if err != nil {
    errMsg := fmt.Errorf("\n[ERROR]:\tTexture Creation failed.\n[E_LOG]:\t%s", err)
    fmt.Println(errMsg)
    return
}
defer tex.Destroy()
}
```

Τρέχοντας το πρόγραμμα, ούτε τωρα θα παρατηρήσετε κάποια αλλαγή. Ένα **σημαντικό πράγμα σχετικά με το rendering** είναι πως επειδή έχετε κάνει κάτι _render_ δεν σημαίνει πως θα το _δείτε_ κι όλας στην οθόνη. Θα πρέπει να τον ανανεώνουμε κάθε φορά που πραγματοποιούμε μία αλλαγή.

#### Φτιάξε ... pixels

##### Resolution

Αρχικά για κάνουμε λίγο _refactor_ το πρόγραμμά μας, έτσι ώστε να μπορούμε εύκολα και γρήγορα να αλλάζουμε την ανάλυση του παραθύρου μας.

```go
const winWidth, winHeight int = 800, 600
```

Καθώς επίσης _καστάρουμε_ σε `int32` τις τιμές πριν καλέσουμε τις SDL functions. Δηλαδή:

```go
// Την CreateWindow
window, err := sdl.CreateWindow("Testing SDL2", sdl.WINDOWPOS_UNDEFINED, sdl.WINDOWPOS_UNDEFINED, int32(winWidth), int32(winHeight), sdl.WINDOW_BORDERLESS)
// Και την createTexture
tex, err := renderer.CreateTexture(sdl.PIXELFORMAT_ABGR8888, sdl.TEXTUREACCESS_STREAMING, int32(winWidth), int32(winHeight))
```

##### Δεσμεύοντας μνήμη για pixel buffer

Για να ζωγραφίσουμε pixels στην οθόνη, πρέπει πρώτα να ξέρουμε τις τιμές τους. Στο συγκεκριμένο παράδειγμα, θα θέσουμε εμείς ό,τι τιμές θέλουμε, αλλά σε κάθε περίπτωση θα πρέπει πρώτα να τις αποθηκεύσουμε στην μνήμη. Χρειαζόμαστε λοιπόν έναν buffer ο οποίος θα κρατάει όλες τις τιμές των pixel μας. Όταν λέω **buffer** εννοώ μία **array** στην οποία αποθηκεύουμε τιμέ τύπου `byte`. Γιατί; Επειδή έτσι το ζητάει η SDL (θα δείτε παρακάτω). Πάμε λοιπόν να φτιάξουμε έναν _pixel buffer_. Όπως και σε κάθε άλλη _array_ έτσι και τώρα, το πρώτο πράγμα που σκεφτόμαστε είναι πόσο μεγάλο πρέπει να είναι το μέγεθός της, έτσι ώστε να δεσμεύσω τον απαιτούμενο χώρο στην μνήμη του υπολογιστή.

Ας πάρουμε ένα απλό παράδειγμα. Ας πούμε λοιπόν ότι η ανάλυση του παραθύρου μας είναι πολύ πολύ μικρή, δηλαδή 4x3 pixels. Αυτό σημαίνει ότι έχουμε 12 pixels. Θυμηθείτε ότι για κάθε pixel έχουμε 4 bytes (βλέπε `sdl.PIXELFORMAT_ABGR8888`). Άρα έχουμε 12 pixels, 4 bytes ανα pixel, μας κάνει 48 bytes! Φτιάχνουμε λοιπόν τον pixel buffer μας:

```go
pixels := make([]byte, winWidth*winHeight*4) // 4*3*4 -> 12*4 -> 48 pixels
```

##### Συνάρτηση αποθήκευσης χρώματος pixel

Θα γράψουμε μία δική μας συνάρτηση η οποία θα λέγεται `setPixel()` και αυτό που θα κάνει είναι να βάζει τιμές σε ένα pixel. Όπως είπαμε και πριν, κάθε pixel αντιπροσωπεύεται από 4 bytes στην μνήμη, όπου το πρώτο είναι το κόκκινο χρώμα, το δεύτερο είναι το πράσινο χρώμα, το τρίτο είναι το μπλέ χρώμα και το τελευταίο byte είναι για την τιμή της alpha. Οπότε καλό θα ήταν φτιάξουμε πρώτα μία `struct` η οποία θα παριστάνει το χρώμα:

```go
type color struct {
    r, g, b, a byte
}
```

Έτσι, μπορούμε να φτιάξουμε πιο όμορφα ένα χρώμα, πχ `color{255, 0, 0, 0}` είναι το κόκκινο, `color{0, 255, 0, 0}` είναι το πράσινο και `color{0, 0, 255, 0}` είναι το μπλε. Στην συνέχεια θα φτιάξουμε την συνάρτηση `setPixel` στην οποία θα δίνουμε 3 πράγματα:

* Την συντεταγμένη x (width) του παραθύρου που βρίσκεται το pixel που θέλουμε να πειράξουμε
* Την συντεταγμένη y (height) του παραθύρου που βρίσκεται το pixel που θέλουμε να πειράξουμε
* Το χρώμα του pixel που θέλουμε να του δώσουμε
* Τον pixel buffer για να αποθηκεύσουμε τις αλλαγές μας

Ένα παράδειγμα κλήσης αυτής, για το πρώτο pixel τέρμα πάνω αριστερά στο παράθυρό μας θα ήταν καπως έτσι:

```go
// Βρες ποιο pixel αντιστοιχεί στις θέσεις [0,0] και κάνε το κόκκινο
setPixel(0, 0, color{255, 0, 0, 0}, pixels)
```

![SDL2 Example](/images/pixelbuffer.png)


Υλοποιώντας την παραπάνω εικόνα, θα είχαμε κάτι τέτοιο:

```go
setPixel(0, 0, color{255, 0, 0, 0}, pixels)
setPixel(1, 0, color{255, 0, 0, 0}, pixels)
setPixel(2, 0, color{0, 255, 0, 0}, pixels)
setPixel(3, 0, color{0, 0, 255, 0}, pixels)

setPixel(0, 1, color{0, 0, 255, 0}, pixels)
setPixel(1, 1, color{0, 0, 255, 0}, pixels)
setPixel(2, 1, color{255, 0, 0, 0}, pixels)
setPixel(3, 1, color{0, 255, 0, 0}, pixels)

setPixel(0, 2, color{0, 255, 0, 0}, pixels)
setPixel(1, 2, color{255, 0, 0, 0}, pixels)
setPixel(2, 2, color{0, 255, 0, 0}, pixels)
setPixel(3, 2, color{0, 0, 255, 0}, pixels)
```

Το μόνο που μένει τώρα είναι η συγγραφή της συνάρτησης:

```go
func setPixel(x, y int, c color, pixels []byte) {
	index := (y*winWidth + x) * 4   // Υπολόγισε την θέση του πρώτου byte του pixel στην θέση x,y
    if index <= len(pixels)-4 && index >= 0 {   // Αν η θέση είναι μικρότερη ή ίση από την θέση του πρώτου byte του ΤΕΛΕΥΤΑΙΟΥ pixel
                                                // και αν η θέση δεν είναι αρνητικός αριθμός
		pixels[index] = c.r     // Βάλε στο πρώτο byte του pixel την τιμή για το κόκκινο χρώμα
		pixels[index+1] = c.g   // Βάλε στο δεύτερο byte του pixel την τιμή για το πράσινο χρώμα
		pixels[index+2] = c.b   // Βάλε στο τρίτο byte του pixel την τιμή για το μπλε χρώμα
		pixels[index+3] = c.a   // Βάλε στο τέταρτο byte του pixel την τιμή για το alpha
	}
}
```

Εφαρμόζοντας τον παραπάνω κώδικα θα έχουμε έναν πίνακα `[]byte` *pixel* με 48 bytes:

```go
pixels[ 0]      pixels[ 1]      pixels[ 2]      pixels[ 3]      pixels[ 4]      pixels[ 5]      pixels[ 6]      pixels[ 7]      pixels[ 8]      pixels[ 9]      pixels[10]      pixels[11]      pixels[12]      pixels[13]      pixels[14]      pixels[15]
pixels[16]      pixels[17]      pixels[18]      pixels[19]      pixels[20]      pixels[21]      pixels[22]      pixels[23]      pixels[24]      pixels[25]      pixels[26]      pixels[27]      pixels[28]      pixels[29]      pixels[30]      pixels[31]
pixels[32]      pixels[33]      pixels[34]      pixels[35]      pixels[36]      pixels[37]      pixels[38]      pixels[39]      pixels[40]      pixels[41]      pixels[42]      pixels[43]      pixels[44]      pixels[45]      pixels[46]      pixels[47]
```

Ο οποίος μοιάζει κάπως έτσι:

```go
[255][0][0][0]          [255][0][0][0]          [0][255][0][0]          [0][0][255][0]
[0][0][255][0]          [0][0][255][0]          [255][0][0][0]          [0][255][0][0]
[0][255][0][0]          [255][0][0][0]          [0][255][0][0]          [0][0][255][0]
```

#### Σύνδεσε τα pixels με το texture

Μέχρι τώρα έχουμε φτιάξει ένα pixel buffer, ο οποίος δεν έχει καμία σχέση με το texture μας. Για να τα συνδέσουμε αυτά τα δύο θα χρησιμοποιήσουμε την συνάρτηση [UpdateTexture](https://wiki.libsdl.org/SDL_UpdateTexture) η οποία μοιάζει κάπως έτσι:

```go
// Update updates the given texture rectangle with new pixel data.
// (https://wiki.libsdl.org/SDL_UpdateTexture)
func (texture *Texture) Update(rect *Rect, pixels []byte, pitch int) error {
	if pixels == nil {
		return nil
	}
	return errorFromInt(int(
		C.SDL_UpdateTexture(
			texture.cptr(),
			rect.cptr(),
			unsafe.Pointer(&pixels[0]),
			C.int(pitch))))
}
```

Σε αντίθεση με τις προηγούμενες συναρτήσεις, η συγκεκριμένη είναι κομμάτι του `tex` που φτιάξαμε πριν και δεν επιστρέφει δύο τιμές, αλλά μόνο μία: `error`. Συνεπώς, όπως σε κάθε Go πρόγραμμα, μετά την κλήση της συναρτησης θα έχουμε το κλασσικό `if err != nil` snippet κώδικα. Πάμε να δούμε τα ορίσματά της:

* rect: Μία [Rect](https://wiki.libsdl.org/SDL_Rect) struct που δεν θα χρησιμοποιήσουμε. Βάζουμε `nil` για να κάνει update ολόκληρη την texture
* pixels: Τα _raw pixel data_, δηλαδή τον pixel buffer που φτιάξαμε προηγουμένως ο οποίο πρέπει να είναι στο ίδιο pixel format (πχ `PIXELFORMAT_ABGR8888`) με αυτόν που επιλέξαμε κατά την δημιουργία της texture.
* pitch: Είναι το `winWidth` του παραθύρου επί τον αριθμό των bytes (4 στην δική μας περίπτωση) που αναπαριστούν ένα pixel. Ουσιαστικα λέει στην SDL πότε να πάει στην επόμενη σειρά.

```go
err = tex.Update(nil, pixels, winWidth*4)
if err != nil {
    errMsg := fmt.Errorf("\n[ERROR]:\tTexture Update failed.\n[E_LOG]:\t%s", err)
    fmt.Println(errMsg)
    return
}
```

#### Κάνε render το texture στο παράθυρο

Έχοντας πλέον _βάψει_ το texture μας όπως θέλουμε, ήρθε η στιγμή να το εμφανίσουμε στην οθόνη. Για να το κάνουμε αυτο πρέπει πρώτα
να αντιγραψουμε το texture στον renderer. Για να το κάνουμε αυτό θα χρησιμοποιήσουμε την συνάρτηση του renderer που λέγεται [Copy](https://wiki.libsdl.org/SDL_RenderCopy) και μοιάζει κάπως έτσι:

```go
// Copy copies a portion of the texture to the current rendering target.
// (https://wiki.libsdl.org/SDL_RenderCopy)
func (renderer *Renderer) Copy(texture *Texture, src, dst *Rect) error {
	return errorFromInt(int(
		C.SDL_RenderCopy(
			renderer.cptr(),
			texture.cptr(),
			src.cptr(),
			dst.cptr())))
}
```

H συγκεκριμένη είναι κομμάτι του `renderer` που φτιάξαμε πριν και δεν επιστρέφει δύο τιμές, αλλά μόνο μία: `error`. Συνεπώς, όπως σε κάθε Go πρόγραμμα, μετά την κλήση της συναρτησης θα έχουμε το κλασσικό `if err != nil` snippet κώδικα. Πάμε να δούμε τα ορίσματά της:

* textre: Το texture μας, δηλαδή `tex`
* srcrect: Δεν χρησιμοποιήσαμε ούτε πριν Rect, οπότε βάζουμε `nil`
* dstrct: Ακριβώς το ίδιο και εδώ.

Ο κώδικας λοιπόν θα είναι:

```go
err = renderer.Copy(tex, nil, nil)
if err != nil {
    errMsg := fmt.Errorf("\n[ERROR]:\tRenderer failed to copy the texture.\n[E_LOG]:\t%s", err)
    fmt.Println(errMsg)
    return
}
```

Τέλος το μόνο που μένει πια είναι να εμφανίσουμε τον renderer στο παράθυρο. Για να το κάνουμε αυτό θα χρησιμοποιήσουμε μία επίσης συνάρτηση του renderer που λέγεται [Present](https://wiki.libsdl.org/SDL_RenderPresent) και μοιάζει κάπως έτσι:

```go
// Present updates the screen with any rendering performed since the previous call.
// (https://wiki.libsdl.org/SDL_RenderPresent)
func (renderer *Renderer) Present() {
	C.SDL_RenderPresent(renderer.cptr())
}
```

H συγκεκριμένη είναι κομμάτι του `renderer` που φτιάξαμε πριν και δεν επιστρέφει καμία τιμή και δεν επιδέχεται κανένα όρισμα. Αυτό που κάνει είναι να ανανεώνει στο παράθυρο τον νέο renderer. Είναι πολύ σημαντικό να γνωρίζετε πως καμία αλλαγή δεν θα εμφανιστεί στην οθόνη αν δεν καλέσετε αυτή την συνάρτηση μετά τις αλλαγές σας.

```go
renderer.Present()
```

Για να δούμε καλύτερα τις αλλαγές μας, προτείνουμε να:

* Μεγαλώσετε την ανάλυση (πχ 800x600)
* Βγάλετε το περίγραμμα από το παράθυρο (βλ _flag_ `sdl.WINDOW_BORDERLESS`)
* Να βάψετε τις γωνίες του παραθύρου

Θα δείτε κάτι τέτοιο:

![SDL2 Example](/images/sdl.png)

Επίσης δοκιμάστε να βάψετε ολόκληρο το παράθυρο ροζ. 

![SDL2 Example](/images/roz.png)

Τελικός κώδικας βρίσκεται στο github repo [drpaneas/sdl2](https://github.com/drpaneas/sdl2/blob/master/sdl2.go)

```go
package main

import (
	"fmt"

	"github.com/veandco/go-sdl2/sdl"
)

const winWidth, winHeight int = 800, 600

type color struct {
	r, g, b, a byte
}

func setPixel(x, y int, c color, pixels []byte) {
	index := (y*winWidth + x) * 4
	if index <= len(pixels)-4 && index >= 0 {
		pixels[index] = c.r
		pixels[index+1] = c.g
		pixels[index+2] = c.b
		pixels[index+3] = c.a
	}
}

func main() {
	window, err := sdl.CreateWindow("Testing SDL2", sdl.WINDOWPOS_UNDEFINED, sdl.WINDOWPOS_UNDEFINED, int32(winWidth), int32(winHeight), sdl.WINDOW_BORDERLESS)
	if err != nil {
		errMsg := fmt.Errorf("\n[ERROR]:\tWindow Creation failed.\n[E_LOG]:\t%s", err)
		fmt.Println(errMsg)
		return
	}
	defer window.Destroy()

	renderer, err := sdl.CreateRenderer(window, -1, sdl.RENDERER_ACCELERATED)
	if err != nil {
		errMsg := fmt.Errorf("\n[ERROR]:\tRenderer Creation failed.\n[E_LOG]:\t%s", err)
		fmt.Println(errMsg)
		return
	}
	defer renderer.Destroy()

	tex, err := renderer.CreateTexture(sdl.PIXELFORMAT_ABGR8888, sdl.TEXTUREACCESS_STREAMING, int32(winWidth), int32(winHeight))
	if err != nil {
		errMsg := fmt.Errorf("\n[ERROR]:\tTexture Creation failed.\n[E_LOG]:\t%s", err)
		fmt.Println(errMsg)
		return
	}
	defer tex.Destroy()

	pixels := make([]byte, winWidth*winHeight*4)

	for y := 0; y < winHeight; y++ {
		for x := 0; x < winWidth; x++ {
			setPixel(x, y, color{255, 100, 90, 0}, pixels)
		}
	}

	err = tex.Update(nil, pixels, winWidth*4)
	if err != nil {
		errMsg := fmt.Errorf("\n[ERROR]:\tTexture Update failed.\n[E_LOG]:\t%s", err)
		fmt.Println(errMsg)
		return
	}

	err = renderer.Copy(tex, nil, nil)
	if err != nil {
		errMsg := fmt.Errorf("\n[ERROR]:\tRenderer failed to copy the texture.\n[E_LOG]:\t%s", err)
		fmt.Println(errMsg)
		return
	}

	renderer.Present()

	sdl.Delay(2000) // Περίμενε για 2 δευτερόλεπτα
}
```