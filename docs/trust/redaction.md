# Redaction

Two passes: the submitter's, on their own device, before anything leaves it; and ours, on the homelab, before anything is extracted. The submitter's pass is the one that matters, because it is the only one that happens before the document crosses the internet.

Tool findings on this page are desk research dated 2026-10-23, checked against each project's release page. None has yet been tested on a real explanation of benefits; that is the open item at the bottom.

## The one rule, stated first

**Redaction must remove the text, not cover it.** A highlight drawn over a PDF leaves the words underneath, selectable and searchable. A blur or mosaic can be reversed. Only an opaque fill, exported as a flat image, is safe. Then check it: zoom in, raise the brightness, and try to select text.

## What to redact and what to keep

An explanation of benefits or itemized bill carries both the identifiers that must go and the figures the site needs.

| Redact | Keep |
|---|---|
| Patient and subscriber names, address, date of birth | Dates of service (we publish month and year only) |
| Member ID, group number, claim number, patient account number, document reference number | Procedure codes (CPT, HCPCS) with modifiers and units; revenue codes on facility bills |
| Provider name, address, NPI, and tax ID (we extract facility and NPI ourselves from the facility list, not from the submitter's document) | Billed, allowed, plan discount, plan paid, deductible, copay, coinsurance, not covered, patient responsibility |
| Barcodes and QR codes, which usually encode the member or claim ID | Network status; plan name and type; remark and reason codes |
| Diagnosis codes, if the submitter prefers; we drop them anyway | |

## Submitter instructions, by device

### Phone, the path most people will use

Take the photo or screenshot, open it in the phone's own editor, cover each identifier with a **solid filled rectangle at full opacity**, save as a copy, and share through a path that drops location data.

- **iPhone.** Use the Markup shape tool, filled, solid black. Do not use the marker or highlighter: it defaults to less than full opacity and the text underneath has been recovered by adjusting exposure ([9to5Mac, 2018](https://9to5mac.com/2018/03/13/ios-markup-reveal-redact-sensitive-info/); [MobileSyrup, 2019](https://mobilesyrup.com/2019/11/28/dont-use-ios-highlighter-tool-censor-information-screenshots/)). When sharing, turn Location off under Options.
- **Android, Google Photos.** The Markup Redact pen added in September 2026 offers only mosaic and blur styles ([9to5Google](https://9to5google.com/2026/09/24/google-photos-redact-moods/)), which are the reversible kind. Use the plain pen at full opacity in a solid colour with overlapping strokes, or a filled shape, and save as a copy.
- **Samsung One UI 8.5 and later.** The share sheet's Privacy Protection detects sensitive text and offers redaction styles ([SamMobile](https://www.sammobile.com/news/one-ui-8-5-can-protect-sensitive-info-in-images-you-share)). Choose the solid style, not blur or pixelate.
- **Krita on Android** ([krita.org](https://krita.org/en/download/), GPLv3) is the one open-source phone editor with a proper opaque-rectangle and flat export, for anyone who wants it.

!!! warning "Unverified"
    Whether current iOS versions still default the marker to partial opacity; per-model availability of Samsung's Privacy Protection; whether iOS screenshots carry location metadata (camera photos do).

### Desktop, the safest path

1. Open the PDF or scan in **LibreOffice Draw** (MPL 2.0; 26.8.x as of October 2026) and use Tools → Redact, or Tools → Auto-Redact with text or regular-expression targets. Export only with **Redacted Export (Black)** or **(White)**, which rasterizes the page so the text is gone. The toolbar's Direct Export to PDF is not a redaction; it keeps the text ([LibreOffice help](https://help.libreoffice.org/latest/en-US/text/shared/guide/redaction.html)).
2. Run the result through **Dangerzone** (Freedom of the Press Foundation, AGPLv3, v0.11.1 of 2026-10-22; Windows, macOS, Linux; needs Docker Desktop or Podman). It renders every page to pixels in a sandbox and rebuilds a new PDF, so nothing but the visible image survives ([dangerzone.rocks](https://docs.dangerzone.rocks/)). It is a flattener, not a redaction tool; it is the last step, not the first.
3. Strip metadata with **mat2** (LGPLv3+, 0.15.0; Linux and macOS, not Windows) or **ExifTool** (`exiftool -all=`; all platforms). ExifTool's PDF metadata removal is reversible by design, so for PDFs prefer mat2 or qpdf after it ([exiftool.org](https://exiftool.org/)).

For an image rather than a PDF: **GIMP** (GPLv3, 3.2.6) with an opaque filled rectangle, exported as PNG or JPEG with every metadata checkbox unticked, then ExifTool.

**Stirling-PDF** (open-core; redaction tools are in the MIT part; v3.2.1 of 2026-10-21) is a reasonable alternative for someone who already runs Docker, with its Auto Redact in text-removal mode or the convert-to-image option ticked ([releases](https://github.com/Stirling-Tools/Stirling-PDF/releases)). We could self-host an instance on the tailnet for volunteers; it is not a path to recommend to residents.

### Browser-only tools

None recommended. No open-source, audited, client-side-only PDF redactor exists as of this writing; the closed ones make self-reported privacy claims, and at least one sends telemetry. Residents should use the phone or desktop paths above.

## Failure modes to warn about

1. **Annotation-only boxes and highlights** (Preview markup, Acrobat Reader comments, most phone PDF apps) sit on a layer above the text. The text stays selectable and the box can be deleted.
2. **Redaction that leaves the text layer**: any tool that only draws a filled rectangle onto a PDF; LibreOffice's Direct Export; older Stirling manual redaction without image conversion. Flatten to an image after redacting.
3. **Semi-transparent markers**: see iPhone above.
4. **Pixelation and blur are reversible.** Depix (2020), Bishop Fox's Unredacter (2022), and UnPixel recover text from mosaic and Gaussian blur ([BleepingComputer](https://www.bleepingcomputer.com/news/security/researcher-reverses-redaction-extracts-words-from-pixelated-image)). Only opaque fills are safe.
5. **Cropping that did not crop.** The 2023 aCropalypse bugs in Pixel Markup (CVE-2023-21036) and Windows Snipping Tool (CVE-2023-28303) left the original image bytes in the file. Patched, but files edited before the patch still leak.
6. **Metadata.** Camera photos carry GPS, device serial, and timestamps; many editors keep them on export. Embedded EXIF thumbnails can hold the unredacted image. Strip with mat2 or ExifTool.
7. **Layered files.** A rectangle on its own layer in XCF, KRA, PSD, or as a PDF annotation can be moved. Export flat.

## Our pass

Every submission, redacted or not, goes through this on the homelab before anyone reads the figures.

| Stage | Tool | Notes |
|---|---|---|
| Rasterize | pdf2image or PyMuPDF at 300 dpi | Everything becomes pixels first, so a PDF text layer cannot leak past us |
| Strip metadata | mat2 | |
| OCR | Tesseract 5.5 (Apache-2.0); PaddleOCR 3.7 as fallback for phone photos | Tesseract is fast and strong on printed scans; PaddleOCR handles skew and photos better at higher compute |
| Detect identifiers | Presidio analyzer (MIT, now at `data-privacy-stack/presidio`) with spaCy `en_core_web_trf` or `lg` | Built-in US_SSN, US_NPI, US_MBI, PHONE_NUMBER, EMAIL_ADDRESS, DATE_TIME, PERSON, LOCATION, CREDIT_CARD |
| Carrier patterns | Presidio `PatternRecognizer`s | Member ID, group, claim, and account numbers; see below |
| Barcodes | zbar via pyzbar | Detect and paint over; they encode IDs |
| Paint | Presidio image redactor | Boxes over every hit on the raster |
| Repository guard | GDD PII guard | Nothing personal reaches Git |
| Human look | The keeper | On the redacted raster and the extracted record, before anything is published |
| Delete | Scheduled | Originals gone after the deletion window; the record notes it |

spaCy's named-entity models are trained on prose; on upper-case tabular EOB text they miss names and mis-type providers, so names are one signal beside the regular expressions and label proximity ("Member ID", "Claim #"), never the only one. Presidio's context-aware scoring boosts a pattern hit sitting next to such a label.

Tools built for clinical narrative (Philter, NLM Scrubber, MIST) are dormant or closed-source and target the wrong document type. Cloud PHI detectors (AWS Comprehend Medical, Google DLP, Azure Language) would send a resident's bill to a third party, which the deletion promise rules out.

### Carrier identifier formats

Publicly documented formats, for the pattern pass. A generic recognizer for an 8–17 character token next to a member, subscriber, group, claim, or account label catches most; these add high-confidence matches.

| Carrier | Format | Source |
|---|---|---|
| Horizon BCBSNJ | Three-character BCBS prefix, then `3HZN`, then digits (`YHV3HZN12345678`) | [Horizon help center](https://www.horizonblue.com/helpcenter/detail/386-what-my-member-id-card) |
| Aetna | `W` + 9 digits, optional 2-digit dependent suffix (`W123456789-01`) | [Cornell HealthSafe ID guide](https://studenthealthbenefits.cornell.edu/sites/default/files/2024-08/HealthSafeID-Guide.pdf) |
| UnitedHealthcare | 9 digits, optional `-00`/`-01` suffix; group 5–8 digits | [UHC sample card](https://retiree.uhc.com/content/dam/retiree/pdf/gvi/2022/get_to_know_your_UnitedHealthcare_Medicare_id_card.pdf) |
| AmeriHealth NJ | 12-digit unique member ID shared across a family; product prefixes Q1A, Q1B, Q1C, Q1H, Q1M and others | [AmeriHealth hospital manual](https://www.amerihealth.com/pdfs/providers/hospital_manual/general_information.pdf) |
| Medicare | 11-character MBI; Presidio's `US_MBI` covers it | |

!!! warning "Unverified"
    Cigna (secondary sources say `U` + 8 digits + 2-digit suffix), Oxford as distinct from UnitedHealthcare, whether AmeriHealth prints the prefix as part of the ID, NJ FamilyCare's 12-digit ID, and every carrier's claim-number format.

## Open

- Test each submitter-side path above on a real EOB and an itemized bill, on an iPhone, an Android phone, and a desktop; then write the one-screen illustrated guide per device.
- Run Presidio with the carrier patterns on redacted and unredacted samples and measure what it misses.
- Confirm the Cigna and Oxford identifier formats.
