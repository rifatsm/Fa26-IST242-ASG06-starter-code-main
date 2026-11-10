Author name: Ri

Project spec:

Objective
In this assignment you will leverage inheritance and polymorphism to build a music-track class hierarchy in Python. You will practice:

definition and implementation of abstract methods (using the abc module);
use of default parameters to achieve constructor-like overloading;
implementation of comparison dunder methods (__lt__, __eq__) to make track objects sortable;
use of inheritance and polymorphism;
identifying common fields/methods across derived classes and pulling them into an abstract base class to improve reuse;
writing clean, well-documented, incremental commits in Git.
The class hierarchy you will build:

  MusicTrack (ABC)
  ┌─────┴─────┐
Song       Podcast
Supporting classes: Artist, Album, Playlist
Collaboration & Deliverables
Item	Details
Incremental commits	Each small, logical change must be its own commit with a clear, descriptive message (e.g., "add Artist class with __str__"). A submission with only one or two giant commits will lose points. Aim for at least 10 meaningful commits.
Deliverable	Submit the URL of your public GitHub repository. The repository must contain all .py source files described below, your partner's Issues (visible on GitHub), your responses/commits addressing them, and a screenshot of all instructor test cases passing.
Getting Started
Create a public GitHub repository named F26_IST242_ASG06_music_<yourname>.
Clone it locally and create the following project structure:
F26_IST242_ASG06_music_<yourname>/
├── artist.py
├── album.py
├── music_track.py
├── song.py
├── podcast.py
├── playlist.py
├── main.py # your main script
└── README.md # brief description, your name, partner's name
Make an initial commit with the empty files and README.md.
Part 1 — Artist and Album
Artist (artist.py)
Create an Artist class that stores:

Field	Type	Description
name	str	The artist or show name
genre	str	The primary genre (e.g., "Hip-Hop", "Journalism")
Provide:

An __init__ that accepts both fields.
Use the single leading-underscore convention (self._name) for all instance attributes to signal they are non-public, and expose each through a @property getter.
A __str__ method that returns:
<name>, <genre>
Example: Kendrick Lamar, Hip-Hop

Album (album.py)
Create an Album class that stores:

Field	Type	Description
title	str	The album or show title
active	bool	Whether the album/show is still active
years	list[int]	The years the album/show released content
Provide:

An __init__ that accepts all three fields. Include a defensive check: if the years list is empty, raise a ValueError with a descriptive message.
Make a defensive copy of years inside __init__ so that mutations to the caller's list do not affect the Album object.
@property getters for each field. The years getter must also return a copy of the internal list.
A debut_year property that returns years[0] as an int — do not store the debut year as a separate field.
A __str__ method that returns:
<title> active = <active>, debut year: <first-year-in-list>
Example: DAMN. active = True, debut year: 2017

Commit your work so far with a clear message.

Part 2 — Stubbing Out the Track Hierarchy
MusicTrack (music_track.py)
Create an abstract class MusicTrack using Python's abc module:

from abc import ABC, abstractmethod
Examine the Song and Podcast classes described below. Identify the common fields between them and store those fields in MusicTrack. Create an __init__ that accepts the corresponding parameters (subclass constructors will call super().__init__(...)).

MusicTrack must provide:

@property getters for each common field you identify.
A release_year property that returns the track's debut year as an int — derived from the Album's years list. Do not pass the release year as a separate constructor parameter.
An abstract method play_time_formatted(self) -> str — returns a human-readable string representation of the track's duration.
A concrete method total_play_time(self, num_plays: int) -> float — returns the total duration in seconds if the track were played num_plays times (based on duration_seconds).
Song (song.py)
Song extends MusicTrack and stores the same three common fields (identify them; they all belong in MusicTrack). Call super().__init__(...) with the appropriate arguments.

Implement play_time_formatted — returns the duration as "MM:SS" (both zero-padded to two digits).

duration_seconds	play_time_formatted()
220	"03:40"
245	"04:05"
65	"01:05"
Override __str__ to return:

(<artist>) <album>, duration: <MM:SS>
Example:

(Kendrick Lamar, Hip-Hop) DAMN. active = True, debut year: 2017, duration: 03:40
Podcast (podcast.py)
Podcast extends MusicTrack and stores one additional field beyond what MusicTrack already provides:

Field	Type	Description
is_explicit	bool	Whether the episode contains explicit content
Its __init__ must accept: artist, album, duration_seconds, and is_explicit with a default value of False (this replaces Java-style constructor overloading). Call super().__init__(...).

Implement play_time_formatted — returns the duration as "HH:MM:SS" (all components zero-padded to two digits), since podcast episodes are typically longer than an hour.

duration_seconds	play_time_formatted()
9000	"02:30:00"
5400	"01:30:00"
3661	"01:01:01"
Override __str__ to return:

(<artist>) <album>, duration: <HH:MM:SS> is explicit: <is_explicit>
Example:

(Joe Rogan, Comedy) The Joe Rogan Experience active = True, debut year: 2009, duration: 02:30:00 is explicit: True
Commit after completing each class.

Part 3 — Making Tracks Comparable
Make MusicTrack sortable by release_year by implementing the following dunder methods inside MusicTrack:

Method	Return value
__eq__(self, other)	True if release years are equal
__lt__(self, other)	True if self.release_year < other.release_year
With __eq__ and __lt__ defined, Python's built-in sorted() and list.sort() will work correctly on mixed lists of Song and Podcast objects.

(Optional but encouraged: use @functools.total_ordering to get <=, >, >= for free.)

Commit this change.

Part 4 — Playlist Class and main.py
Playlist (playlist.py)
The Playlist class stores a single private field _tracks (a Python list). Provide:

Method / Property	Description
add_track(track)	Appends a MusicTrack to the list. Returns None.
clear_playlist()	Clears the list using .clear() — do not set the list to None. Returns None.
sort_by_release_year()	Sorts _tracks in place (call self._tracks.sort()).
__str__	Returns each track on its own line.
tracks (property)	Returns a copy of the internal list (protects encapsulation).
main.py
Write a main.py script (with an if __name__ == "__main__": guard) that:

Creates the following tracks (use the durations provided):
Track	Artist	Genre	Album Title	Active	Years	Extra
Song	Kendrick Lamar	Hip-Hop	DAMN.	True	2017–2018	220 sec
Song	Alanis Morissette	Alternative	Jagged Little Pill	False	1995–1996	245 sec
Podcast	Joe Rogan	Comedy	The Joe Rogan Experience	True	2009–2010	9000 sec, explicit
Podcast	Sarah Koenig	Journalism	Serial	False	2014–2015	5400 sec, not explicit (default)
Instantiates a Playlist named p, adds all four tracks.
Prints "Before sorting:" followed by p.
Calls p.sort_by_release_year().
Prints "After sorting:" followed by p.
Expected output:

Before sorting:
(Kendrick Lamar, Hip-Hop) DAMN. active = True,  debut year: 2017, duration: 03:40
(Alanis Morissette, Alternative) Jagged Little Pill active = False,  debut year: 1995, duration: 04:05
(Joe Rogan, Comedy) The Joe Rogan Experience active = True,  debut year: 2009, duration: 02:30:00 is explicit: True
(Sarah Koenig, Journalism) Serial active = False,  debut year: 2014, duration: 01:30:00 is explicit: False

After sorting:
(Alanis Morissette, Alternative) Jagged Little Pill active = False,  debut year: 1995, duration: 04:05
(Joe Rogan, Comedy) The Joe Rogan Experience active = True,  debut year: 2009, duration: 02:30:00 is explicit: True
(Sarah Koenig, Journalism) Serial active = False,  debut year: 2014, duration: 01:30:00 is explicit: False
(Kendrick Lamar, Hip-Hop) DAMN. active = True,  debut year: 2017, duration: 03:40
Commit your main.py and any final polishing.

Developer Notes: work in progress (Nov 10, 2026).