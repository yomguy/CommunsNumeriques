class: center, middle, ircam, inverse

# TimeSide

##Python audio processing framework and server made for the web

###Guillaume Pellerin, Antoine Grandry, Martin Desrumaux - POW - IRCAM

#### Séminaire STMS - 14/10/2020 - IRCAM

<img src="img/POW_MB.png" height="100px" />

---
class: ircam

#Outline

- Intro (GP 2mn)
- History (GP 5mn)
- Core (GP 10mn)
- Server (AG 10mn)
- Player v2 (MD 10mn)
- Perspectives (GP 3mn)


---
class: ircam, tight

#TimeSide

## Context

- digization > big data > platforms > machine learning > users & listeners
- more data sets, databases, formats, algorithm versions, open DSP libraries & communities
- collaborative workflows, citizen sciences through the web

## Problems, needs and usecases

- computer science need human data, digital humanities needs computers
- difficult to port and scale some algorithms on streaming platforms (DSP at scale)
- copyrighted data
- reproducible research
- sustainable digital archiving
- open source and standards
- format evolution and abstraction
- duplicate and version everything
- access everywhere

---
class: ircam

#TimeSide

##History

- 2007 : [Telemeta](https://telemeta.org) developed for the sound archives of the CNRS / Musée de l'Homme
- 2010 : TimeSide separation as a autonomous library and then a framework with a plugin oriented architecture
- 2011 : Telemeta v1.0 released and http://archives.crem-cnrs.fr/
- 2013 : DIADEMS project (ANR) : Processor API, external plugins
- 2015 : TimeSide API and server prototype
- 2015 : KAMoulox, DaCaRyh projects (ANR)
- 2016 : WASABI (ANR), CREM-NYU-Parisson (Arabic rythm analysis)

---
class: ircam

# Telemeta - CREM

<center><img src="img/telemeta_english.png" width="80%"></center>

.center[http://archives.crem-cnrs.fr/]

---
class: ircam

# Telemeta - CREM

<center><img src="img/telemeta_geo.png" width="80%"></center>


---
class: ircam, tight
#Telemeta / TimeSide integration

.pull-left[
<img src="img/telemeta_logo.png" height="100px" />

###Collaborative multimedia asset management system

MIR + Musicology + Archiving = MIRchiving

### Ecosystem

- 20 public partners
- 15 historical developers (6000 commits)
- 500 users (CREM)
- and thousands of developers! (open source community)
- mutualized development model

]

.pull-right[
.right[![image](img/telemeta_screenshot_en_2.png)]
<br>

https://github.com/Parisson/Telemeta
]



---
class: ircam, tight

#WASABI project

##Web Audio and SemAntics in the Browser for Indexation

- 42 months from 2016 Q4 to april 2020 Q4
- 750 k€ project granted by the french Research National Agency

## Consortium

- INRIA (I3S)
- IRCAM (APM, AnaSyn, POW)
- Deezer R&D
- Radio France
- Parisson


---
class: ircam

#WASABI project

##Objectives

- Propose some new methodologies to index music in the web and audio contexts
- Link semantics (linked metadata) + acoustics (MIR data) + machine learning
- Develop and publish some open source web services through original APIs

##Use cases

- enhanced web music browsing
- data journalism
- music composing through big data (schools)
- desambiguisation
- lyrics synchronization

---
class: ircam
# WASABI platform ([link](https://wasabi.i3s.unice.fr/))

.pic-container[
    <img src="img/wasabi.i3s.unice.fr-ledzep-1.png" width="100%">
]

---
class: ircam
# WASABI platform

.pic-container[
    <img src="img/Architecture_WASABI.png" width="80%">
]


---
class: center, middle, ircam, inverse

# TimeSide Core


---
class: ircam, tight

#TimeSide

## Python audio processing framework and server made for the web

https://github.com/Parisson/TimeSide

##Goals

* **Process** audio fast and asynchronous with **Python**,
* **Decode** audio frames from *any* audio or video media format into **Numpy arrays**,
* **Analyze** audio content with some **state-of-the-art** audio feature extraction libraries like **Aubio, Essentia, Librosa, Yaafe, VAMP** and pure python processors
* **Visualize** audio data with various fancy waveforms, spectrograms and other cool graphers,
* **Transcode** audio data in various media formats and stream them through web apps,
* **Serialize** feature analysis data through various portable formats (XML, JSON, HDF5)
* **Playback** and **interact on demand** through a smart high-level **HTML5 extensible player**,
* **Index**, **tag** and **annotate** audio archives with **cultural and semantic metadata**,
* **Deploy** and **scale** your own audio processing engine flawlessly through any infrastructure with **Docker**


---
class: ircam

#TimeSide

##Python audio processing framework and server made for the web

https://github.com/Parisson/TimeSide

##Use cases

- Scaled audio processing (filtering, transcoding, machine learning, etc...)
- Audio process prototyping
- Audio dynamic visualization
- Automatic segmentation and labelling synchronized with audio events
- Collaborative annotation
- Audio web services

##License AGPL v2

---
class: ircam

#timeside.core

.pull-left-30[

##API & architecture

- streaming oriented core engine
- data persistence

]

.pull-right-70[
.right[![image-wh-bg](img/TimeSide_pipe.svg)]
]

---
class: ircam

.pull-left-30[

#timeside.core

##API & architecture

- streaming oriented core engine
- data persistence
- processing API
- plugin architecture
- namespace
]

.pull-right-70[
```python
class DummyAnalyzer(Analyzer):
    """A dummy analyzer returning random samples from audio frames"""
    implements(IAnalyzer)

    @interfacedoc
    def setup(self, channels=None, samplerate=None, blocksize=None, totalframes=None):
        super(DummyAnalyzer, self).setup(channels, samplerate, blocksize, totalframes)
        self.values = numpy.array([0])

    @staticmethod
    @interfacedoc
    def id():
        return "dummy"

    @staticmethod
    @interfacedoc
    def name():
        return "Dummy analyzer"

    @staticmethod
    @interfacedoc
    def unit():
        return "None"

    def process(self, frames, eod=False):
        size = frames.size
        if size:
            index = numpy.random.randint(0, size, 1)
            self.values = numpy.append(self.values, frames[index])
        return frames, eod

    def post_process(self):
        result = self.new_result(data_mode='value', time_mode='global')
        result.data_object.value = self.values
        self.add_result(result)

```
]


---
class: ircam

# timeside.core

.pull-left-30[

##API & architecture

- streaming oriented core engine
- data persistence
- processing API
- plugin architecture
- namespace
- docker packaged
- ~500 unit tests
]

.pull-right-70[
```python
import timeside.core
from timeside.core import get_processor
from timeside.core.tools.test_samples import samples

wavfile = samples['sweep.wav']
decoder  =  get_processor('file_decoder')(wavfile)
grapher  =  get_processor('spectrogram')()
analyzer =  get_processor('level')()
encoder  =  get_processor('vorbis_encoder')('sweep.ogg')

pipe = (decoder | grapher | analyzer | encoder)
pipe.run()

grapher.render(output='spectrogram.png')
print('Level:', analyzer.results)
Level: {'level.max': AnalyzerResult(...), 'level.rms': AnalyzerResult(...)}
```

```bash
$ git clone --recursive https://github.com/Parisson/TimeSide.git
$ docker-compose up
```

]


---
class: ircam, tight

# timeside.core

##Plugin examples

.pull-left[
- FileDecoder, ArrayDecoder, LiveDecoder, AubioDecoder
- VorbisEncoder, WavEncoder, Mp3Encoder, FlacEncoder, OpusEncoder, etc.
- WaveformAnalyzer, SpectrogramAnalyzer
- AubioTemporal, AubioPitch, etc.
- Yaafe wrapper (graph oriented)
- librosa (function oriented)
- VampPyHost
- Essentia bridge
]

.pull-right[
- Speech detection
- Music detection
- Singing voice detection
- Monophony / Polyphony
- Dissonance
- Timbre Toolbox
- etc... (experimental)

https://github.com/DIADEMS/timeside-diadems

]


---
class: ircam, tight

# timeside.core

## What's new?

0.9.4 > 1.0.0a (released yesterday!): 674 commits, 7 contributors

* Python 2.7 to 3.7
* Drop GStreamer for Aubio as default decoder and encoder
* Add core and server processors' versioning and server process' run time
* Regroup all dependencies on pip requirements, drop conda
* Server refactoring:
  * audio process run on items (REST API track's model)
  * several tools, views, models and serializers
  * REST API's schema on OpenAPI 3 specification and automatic Redoc generation
* Upgrade Django to 2.2, Django REST Framework to 3.11, Celery to 4.4
* Add `provider` as a core API component and as a REST API model
* Add provider plugins `deezer-preview`, `deezer-complete` and `youtube`
* Improve server unit testing
* Add JWT authentication on REST API
* A lot of bug fixes
* Add core, server and workers logging

---
class: ircam, middle, center, inverse

#TimeSide Server


---
class: ircam, tight
# timeside.server

.pull-left[
## RESTful API built on TimeSide
https://sandbox.wasabi.telemeta.org/timeside/api/

### Server design
- Based on Django REST Framework (DRF)
- Interoperability between other servers or frontends and TimeSide instance and its data
- Object-relational database in order to store music tracks and processing results
- Models: essential fields and behaviors of stored data
- queue-worker architecture enables to run tasks asynchronously

]

.pull-right[
<img src="img/server_api.png" width="520">
]

---
class: ircam, tight
# timeside.server

.pull-left[
## RESTful API built on TimeSide
https://sandbox.wasabi.telemeta.org/timeside/api/

### What's new on server?
- Add audio providers (Deezer, Youtube)
- Switch from MySQL to PostgreSQL
- Add a JWT authentication
- Make the API follow the OpenAPI specification
- Build a TypeScript SDK on the REST API
- Add several tools, views, models and serializers
- Improve server unit testing
- Fix few bugs
- Python, Django, DRF and Celery upgrades

]

.pull-right[
<img src="img/server_api.png" width="520">
]

---
class: ircam, tight
#timeside.server

.pull-left[
## RESTful API built on TimeSide
https://sandbox.wasabi.telemeta.org/timeside/api/

### Use cases
- **Upload** audio tracks
- **Retrieve** audio tracks from remote providers
- **Stream** original or transcoded sources
- **Run** on-demand analysis
- **Customize** processors parameters
- **Collect** track's annotation to build audio corpora
- **Deliver** and share several types of results:
    - transcoded audio
    - serialized analysis as JSON or image
- **Export/import** an audio analysis dataset
]

.pull-right[
<img src="img/server_api.png" width="520">
]

---
class: ircam, tight
# timeside.server

.pull-left[
## RESTful API built on TimeSide
https://sandbox.wasabi.telemeta.org/timeside/api/

### Models

- `Item`: audio content and metadata (external id)
- `Provider`: provide audio from a given plateform
- `Selection`: list of items (corpus)
- `Processor`: plugins with version and default parameters
- `Preset`: processor and a set of parameters
- `Experience`: list of presets forming a pipe
- `Task`: an experience and a selection
- `Result`: transcoded audio or numerical outputs (hdf5 file)
- `Annotation`: label audio file on a given time or segment

]

.pull-right[
<img src="img/server_api.png" width="520">
]


---
class: ircam
#timeside.server

## RESTful API documentation
https://sandbox.wasabi.telemeta.org/timeside/api/docs/

<img src="img/server_doc.png" width="900">


---
--
class: ircam
#timeside.server

.pull-left[
## Workflow examples in WASABI Project

### With providers

- Youtube besed on `youtube-dl`
- Deezer 30 seconds preview consuming Deezer's API

]


.pull-right[
<img src="img/architecture_WASABI.png" width="450">
<!-- .right[![image-wh-bg](img/architecture_WASABI.png)] -->
]

---

class: ircam
#timeside.server

.pull-left[
## Workflow examples in WASABI Project

### With server

POC of a webservice delivering audio analysis to another remote server:
a large database of musical metadata

]


.pull-right[
<img src="img/architecture_WASABI.png" width="450">
<!-- .right[![image-wh-bg](img/architecture_WASABI.png)] -->
]

---
class: ircam
#timeside.server

.pull-left[
## Workflow examples in WASABI Project

### Import/export of a run on Deezer's infrastructure

keep audio "at home"



]


.pull-right[
<img src="img/architecture_WASABI.png" width="450">
<!-- .right[![image-wh-bg](img/architecture_WASABI.png)] -->
]


---
class: ircam
#timeside.server

.pull-left[
## Workflow examples in WASABI Project

### With a frontend player



]


.pull-right[
<img src="img/architecture_WASABI.png" width="450">
<!-- .right[![image-wh-bg](img/architecture_WASABI.png)] -->
]


---
class: ircam, middle, center, inverse

#TimeSide Player


---

class: ircam
#timeside.player

.pull-left-30[
## v1 (SoundManager2)

- on demand processing
- simple marker annotation
- bitmap image cache only
]

.pull-right-70[
.right[![image](img/SOLO_DUOdetection.png)]
]

---
class: ircam

.pull-left[
#timeside.player

##v2

###Constraints

- Handling multiple hours audio files
- Multiple user annotations and analysis tracks
- Analysis rendered on the frontend
- Zooming
]

.pull-right[
.right[![image](img/ui-telemeta-grand-2_1.png)]
]


---
class: ircam, tight

#timeside.player

##API SDK (client library)

.pull-left[
- Timeside API: 75 routes
- openapi-generator
	- Typescript
	- Fetch
	- OpenAPI v3 Schemas
- Improve schema support on DRF (PR)
	- Components
	- Customize default names
- Glue code
	- Authentication
	- Initialization on Browser / Node
- Documentation
]


.pull-right[
```yaml
  /timeside/api/analysis/:
    get:
      operationId: listAnalysis
      parameters: []
      responses:
        '200':
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Analysis'
```
]


- SDK: https://github.com/Ircam-Web/timeside-sdk-js
- Node: https://github.com/Ircam-Web/timeside-scripts

Opportunity: `openapi-generator` also supports Python, C/C++, Ruby, Go, Rust etc...

---
class: ircam, tight

#timeside.player

##Environment

🔧 Technologies
- Vue (composition-api): DOM Manipulation, Data reactivity
- D3 (SVG): Render waveform / Analysis
- HTML5 Audio
- Web Animations API
- Resize Observer
- Github Action for continuous test & deployment (npm, gh-page)
- Github: https://github.com/ircam-web/timeside-player

🚀 Usage
- Standalone app
- Web library
	- React
	- Vue
	- HTML

🌐 Tested on Firefox & Chrome

---
class: ircam, middle, center

# Demo time!

---
class: ircam, tight

#Perspectives

## Audio processing web service (SaaS)

###TODO

- Clustering and orchestration (Kubernetes)
- Implementing Websocket, ServerEvent or Webhook to avoid task status polling
- Documentation, notebooks
- More tests

###Use cases

- MIRchiving (Telemeta 2, CMS embedding, BNF, UNAM, UNESCO)
- Metadata enhanced streaming services (Spotify, Deezer, SoundCloud, Netflix)
- Digitization and media packaging services (VectraCom, VDM, Gecko)

###Dual licencing

- open source community release of the core framework (AGPL)
- proprietary (entreprise) release (SATT Lutech / Parisson / IRCAM Amplify)


---
class: center, middle, ircam, inverse

# Merci !

##Guillaume Pellerin, Antoine Grandry, Martin Desrumaux - IRCAM, France

<img src="img/wasabi_logo_darkbg.png" height="75px" />

###guillaume.pellerin@ircam.fr / @yomguy / @telemeta


---
class: ircam, tight

#Other TimeSide related projects

- DIADEMS
- DaCaRyh (Labex) - Steel band history
  - C4DM : Centre for Digital Music at Queen Mary University (London, UK)
  - CREM
  - Parisson
- NYU / CREM - Arabic rythm analysis
  - NYU : New York University
  - CREM
  - Parisson
- KAMoulox (ANR) - audio source separation
    - INRIA
    - Parisson
    - http://kamoulox.telemeta.org/
    - http://kamoulox.telemeta.org/timeside/api/results/

