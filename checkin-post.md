# Mid-Module Check-In: Late Night Radio

## Interaction Concept

It's 1 AM and you're alone with an old radio. You have to *find* someone out there.

The interface is a bright pixel-art radio. The controls aren't in a panel. They're **paper notes clipped to a clothesline**, swaying gently, swinging when you touch them, and dancing faster when a station locks in.

- **Tuning note:** slider (88–108 FM) plus ◀ ▶ buttons. You can also use the arrow keys or scroll on the radio's dial to nudge.
- **Antenna note:** slider (0–180°). You can also **drag the actual antenna** on the radio.
- **Volume note:** slider
- **Time note:** pick 11 PM / 1 AM / 3 AM. The pixel sky changes from a sunset to purple to electric blue.
- **Call-in note:** type a message to the night host and hit SEND. The host's reply gets written back onto the note.
- **Power note**, and one weird input: **click the speaker (or press Space) to smack the radio.**

A second clothesline is the **Station Log**: five blank "?" notes. Every time you hold a station clearly for a moment, its note **drops in and clips onto the rope**. Click any note to **flip it over**. Undiscovered notes give a hint on the back, and found ones show a line you heard. Find all five and you get pixel confetti.

No single input does much on its own. The feeling comes from how they combine:

| Combination | What happens |
|---|---|
| Frequency + antenna | Together they set **signal strength**. The right frequency with the wrong antenna angle still sounds weak. |
| Signal + volume | Volume mixes **static and music** in real time (Web Audio). Weak signal means hiss and a wobbly, warped chord. Strong signal means clean music. |
| Signal + screen text | Every letter on the display has a chance of turning into `#%~*` noise, depending on signal strength. You literally read through the static. |
| Signal + call-in text | The host only hears what gets through. With a clear signal: *"...beautifully said."* With a bad one, the host reads your garbled message back seriously: *"caller says '·s a#y·ne ~lse'... wow. deep. very deep. next caller."* |
| Time + drift | The signal **drifts** off over time, so you have to keep nudging the dial. Later at night means more drift. |
| Time + frequency + antenna | **Secret station:** at 3 AM only, on 99.9 with the antenna pointing straight up, a hidden station called *the quiet hour* appears. |
| Holding a clean signal | If you keep a locked signal for a while, **warmth** builds. The whole radio starts to glow orange, the host speaks to you directly ("to whoever has stayed tuned this long — thank you"), and the drift slows down. |

## What It Represents

I wanted the feeling of being awake when everyone else is asleep: lonely at first, then strangely comforting once you find a voice.

- **Satisfying:** the moment the static drops out and the text sharpens into real words. Locking a station feels earned because you got two inputs (frequency and antenna) right together.
- **Delightfully frustrating:** the drift. You can't set it and forget it. It slowly slips away and you have to tend it. Smacking the radio *usually* fixes it (60%), but sometimes makes it worse, just like a real one.
- **Emotional payoff:** warmth is a reward for **patience and attention**, not clicking. The system gets calmer (less drift) the longer you stay with it. That's the emotional idea I care most about: *being listened to happens when you keep listening.*
- **Funny misunderstanding:** the call-in host misreads you on purpose when the signal is bad. Your input is "heard" only as clearly as you've tuned.

## Logic Map (pseudocode)

```
every 200ms:
  drift += small random amount (bigger at 3am, smaller when warm)
  heard = frequency + drift

  for each station available right now (99.9 only if time == 3am):
    freqScore = closeness of heard to station.freq
    antScore  = closeness of antenna to station.angle
    signal    = freqScore^1.5 * antScore
  pick the strongest station

  if signal > 0.8: warmth goes up
  else:            warmth goes down

  mood = off | lost | almost | locked | warm
  audio: static volume = (1 - signal), music volume = signal
  text:  each character survives with probability ≈ signal
```

## UI Design Decisions

- **Pixel art, but vivid.** "Late night" usually means dark and moody, but I wanted it to feel *hopeful*, not gloomy. So the sky is banded hot pink, purple, and electric blue, the radio is bubblegum pink, and the static is actual chunky pixels on a Game Boy-green screen.
- **Notes on a clothesline.** Controls hung on a rope feel handmade and alive. They sway on their own like there's a breeze through an open window. Interacting makes them swing, so every input gets physical feedback.
- **Station Log as a collection.** Clipping a new note onto the rope gives a small "got it!" moment and makes discovery visible. The flip-for-a-hint backs solve a problem I had earlier: nobody would ever find the secret 3 AM station without a clue.
- **Two ways to do the same thing.** You can drag the antenna or use the slider, and nudge with buttons, arrows, or scrolling. Direct manipulation (grabbing the antenna) feels better, while the note controls are more precise.
- **Slider for tuning, antenna for angle.** Both are continuous inputs, because I wanted the user to *search*, not pick. A dropdown of stations would remove the whole feeling.
- **Buttons for time of night.** Time is a choice of setting, not something you fine-tune, so three discrete options fit.
- **Text input for calling in.** It's the only place you "speak," and the system answers back through the same noisy channel.
- **Keyboard: arrows and Space.** Arrows let you nudge more precisely than dragging. Space as a "smack" adds a physical, slightly unreliable gesture.
- **Signal bars and a mood label** ("lost / almost / locked / warm") make the hidden state visible, so the user can tell they're getting closer.

## AI Support

I used an AI coding assistant to help build the prototype and the signal math.

- **Prompt idea that worked:** *"Make a signal strength value from two inputs, frequency and antenna angle, where being right on only one of them gives a weak signal, and both together gives a strong one."* Multiplying the two scores (instead of adding them) was the key. Adding them let you get a decent signal with just one input correct, which felt wrong.
- **Bug we hit:** in the first version the drift was far too strong. After about 20 seconds the signal drifted so far that **no station could lock even when the dial and antenna were right.** That felt broken, not "delightfully frustrating." We cut the drift amount and made it so a *big* dial swing (hunting for a new station) resets the drift, while *small* nudges count as fighting the drift.
- **Second tuning issue:** warmth reset too harshly. Any tiny signal dip wiped out progress, so 30 seconds of careful tuning still didn't reach the warm state. We softened the decay so brief dips cost only a little.
- **Takeaway:** the AI was great at the audio setup (white noise buffer, oscillators, gain mixing), but "how it *feels*" only came out of actually playing it and adjusting numbers.

## Still Figuring Out

- **Balancing the drift:** where's the line between "I want to keep tending this" and "this is annoying"? Maybe it should depend on the station, e.g. the numbers station drifts the most because it's eerie.
- **Are the hints too generous?** Flipping a "?" note tells you nearly exactly where a station is. Maybe hints should unlock only after you've struggled for a while.
- **What happens after warmth?** Should the host remember your call-in message later, so the radio feels like a relationship rather than a single moment?
