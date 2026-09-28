# Technovator 360 Viewer

Static pages for the UMass Boston Virtual Experience Platform (VEP). They run inside the
SharePoint site `https://umassboston.sharepoint.com/sites/virtual-experience` and read and
write its lists through the SharePoint REST API, using the visitor's own Microsoft 365 sign-in.

| Page | What it does |
| --- | --- |
| `index.html` | Sends visitors to the gallery (for hosts that open `index.html` at the site root). |
| `gallery.html` | Lists published tours, with search and a department filter. |
| `viewer.html?tourId=N` | Shows a tour in [Pannellum](https://pannellum.org/): scene links, info hotspots, map, descriptions. |
| `editor.html?tourId=N` | Creates tours, uploads 360° photos as scenes, and places info hotspots by clicking the photo. |

## Deploying

Upload the three `.html` files to the `VEPAssets` document library, next to each other.
The gallery links to `VEPAssets/viewer.html` and `VEPAssets/editor.html` (see `CFG` at the top of
each page's script if these paths change).

Viewing needs read access to the site. Using the editor needs **Contribute** (edit) access to the
lists below and to the `360Photos` library.

## SharePoint lists

Existing lists used by the viewer and gallery: `VirtualTours`, `TourScenes`, `SceneConnections`,
and the `360Photos` document library.

The editor adds one list, which you create once in the site (**New → List**, name `SceneHotspots`):

| Column | Type | Notes |
| --- | --- | --- |
| `Title` | Single line of text (built in) | Hotspot name, shown on hover. |
| `InfoText` | Multiple lines of text, **plain text** | The information shown when the hotspot is opened. |
| `TourID` | Number | Id of the `VirtualTours` item. |
| `SceneID` | Number | Id of the `TourScenes` item. |
| `HotspotPitch` | Number, 1 decimal place | Up/down angle, set by the editor. |
| `HotspotYaw` | Number, 1 decimal place | Left/right angle, set by the editor. |

Use exactly these internal names. If you create a column with a different display name first,
SharePoint keeps the first name as the internal one. The viewer treats a missing `SceneHotspots`
list as "no info hotspots", so tours keep working before the list exists.

Photos uploaded by the editor go into `360Photos` with a timestamp prefix, and the new scene's
`PhotoUrl` is set to the file's server-relative URL. The editor assumes the library's URL name is
`360Photos`; change `photosLibrary` in `editor.html` if yours differs.

## Photos

Use equirectangular JPG or PNG photos, twice as wide as they are tall (the normal export from 360°
cameras). Photos wider than 8192 px may not open on phones and older laptops.

## Trying it without SharePoint

Opened anywhere other than the SharePoint site, or with `?demo` in the address, the pages use
built-in sample data. In the editor, demo changes (including uploaded photos) are kept only until
the page is reloaded. For example, run `python3 -m http.server` in this folder and open
`http://localhost:8000/editor.html`.
