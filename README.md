#go-windows-session-notifications#

Receive session change notifications from Windows.

##Sample code##

```go
package main

import (
	"github.com/brunoqc/go-windows-session-notifications"
	"log"
)

func main() {
	quit := make(chan int)

	changes := make(chan session_notifications.Message, 100)
	closeChan := make(chan int)

	go func() {
		for {
			select {
			case c := <-changes:
				switch c.Param {
				case session_notifications.WTS_SESSION_LOCK:
					log.Println("session locked")
				case session_notifications.WTS_SESSION_UNLOCK:
					log.Println("session unlocked")
				}
				close(c.ChanOk)
			}
		}
	}()

	session_notifications.Subscribe(changes, closeChan)

	// ctrl+c to quit
	<-quit
}
```

##Build with##

```bash
set CGO_LDFLAGS=-lwtsapi32

# with powershell
$env:CGO_LDFLAGS="-lwtsapi32";

go build
```
