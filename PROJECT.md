# Melody Lift — Project Charter

**Status: APPROVED on 2026-11-10.** Nothing is built yet.

This file is public, so the exact server names and web address are left out.

## 1. The one thing it must do

Upload a .wav or .mp3 of a song on a web page and get back readable, printable sheet music of the **vocal melody**, written for **violin**, with other instruments one click away.

## 2. What "working" must include

- The score is the lead vocal line, taken from a normal full recording. Not the accompaniment, and not every sound in the track.
- It reads like real sheet music: bars, a time signature, a key signature and sensible note lengths.
- It is playable on the chosen instrument: correct clef, correct transposition, and no note below the instrument's lowest note.
- Upload to printable page happens entirely on the web page. No command line and no second program.

## 3. Off-limits workarounds

- "Download the MIDI and open it in MuseScore" as the way to get sheet music.
- Asking for an already-isolated vocal track.
- Requiring tempo, key or time signature to be typed in before it works. Optional corrections afterwards are fine.
- Sending the audio to a paid outside transcription service.
- Models licensed for non-commercial use only. This rules out Sheet Sage's models.
- Pinned Docker images.

## 4. Where it runs and where backups go

- **Runs as:** one Docker container on the home server, image tag `:latest`, built by GitHub Actions. Nothing installs on your PC.
- **Address:** a private subdomain, through the reverse proxy, with single sign-on in front. Private: you and the people you let in.
- **Code:** a public GitHub repo, github.com/TeeJS/melody-lift, cloned in your folder `D:\Github\melody-lift`. Git history is the backup for code.
- **Uploaded songs and finished scores:** kept in the container's data folder on the server. They get no separate backup, because any score can be regenerated from the song file.
- **Hardware:** built to run on CPU. How long a 4-minute song takes on the server is unknown until the proof run.

## 5. How we know it is done

1. **Proof run first, before any app is built.** I run the pipeline on 3 songs you pick and hand you the violin PDFs. You decide whether they are a usable draft. If not, we change the model or stop.
2. In the browser, uploading one .mp3 and one .wav each produces a violin score on the page, with no other tool.
3. "Download PDF" gives a file that prints correctly on Letter paper.
4. Switching instrument redraws the score in the correct clef and transposition without uploading again. Example: the B♭ clarinet part reads a whole step higher than the violin part.
5. No note in the violin score is below G3, the violin's lowest note.
6. A recording of a tune with a known score comes back in the correct key and time signature, with at least 9 of every 10 notes on the correct pitch. This bar is my proposal and can move after the proof run.
7. The container starts from its template on the server and the page loads at its address.

## Scope

**In version 1**

- Instruments: violin, viola, cello, flute, B♭ clarinet, B♭ trumpet, alto sax.
- Downloads: PDF, MusicXML, MIDI.
- Whole-song fix buttons: octave up or down, change key, halve or double the note lengths.

**Not in version 1**

- Lyrics under the notes, chord symbols, harmony or backing vocals.
- Editing single notes in the browser.
- Pulling audio from YouTube or other links.

**Honest limit:** version 1 will get some notes wrong, and it cannot fix a single wrong note on the page. The fix for that is opening the MusicXML download in MuseScore. The app still produces the printable sheet by itself; MuseScore is only for hand-correcting. If the proof run shows many wrong notes, in-browser note editing becomes phase 2.

## Future phases

**User sign-up and user database.** Starts once phase 1 (version 1) is working. People create their own account on a sign-up page, and a user database stores the accounts.

Two things are decided when this phase starts, not now:

- How sign-up fits with the single sign-on that phase 1 uses.
- Where the user database is backed up. Phase 1 holds nothing that needs a backup; this phase does.

## Defaults I picked

Tell me if any of these is wrong. Otherwise they stand.

| Default | Why |
|---|---|
| Hosted on the home server, not on your PC | The models need Python and PyTorch, which are several GB. Keeping that on the server keeps your managed PC clean. |
| Private, behind single sign-on | Uploaded songs are usually copyrighted. A private tool avoids hosting them publicly. |
| The 7 instruments listed under Scope | music21 already has the clef, range and transposition for each one. |
| Single-note editing waits for phase 2 | It is a large, separate build. The proof run tells us whether it is needed. |

## Building blocks

Licenses and activity were read from GitHub on 2026-11-10. The biggest risk is turning expressive singing into clean, readable rhythm, which is why the proof run comes first.

| Stage | Project | License |
|---|---|---|
| Isolate the vocals | [python-audio-separator](https://github.com/nomadkaraoke/python-audio-separator) | MIT |
| Vocals to notes | [GAME](https://github.com/openvpi/GAME) | MIT |
| Find the beat, for bar lines | [beat_this](https://github.com/CPJKU/beat_this) or [librosa](https://github.com/librosa/librosa), chosen in the proof run | MIT / ISC |
| Notes to score, transposing | [music21](https://github.com/cuthbertLab/music21) | BSD-3 |
| Show the score in the browser | [OpenSheetMusicDisplay](https://github.com/opensheetmusicdisplay/opensheetmusicdisplay) | BSD-3 |
