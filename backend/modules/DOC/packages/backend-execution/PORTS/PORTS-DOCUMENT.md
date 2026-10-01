<!-- source: PHASE:PORTS / SUB:PORTS-DOCUMENT -->
<!-- context: PORTS-HEADER.md — phase-level preamble -->
<!-- traces: REQ-DOC-005, REQ-DOC-007, REQ-DOC-008, REQ-DOC-009, REQ-DOC-011, REQ-DOC-015, REQ-DOC-024, REQ-DOC-025, REQ-DOC-026, REQ-DOC-028, REQ-DOC-029, REQ-DOC-041, REQ-DOC-042, REQ-DOC-049, REQ-DOC-050 -->
<!-- SUB:PORTS-DOCUMENT:START traces=REQ-DOC-005,REQ-DOC-007,REQ-DOC-008,REQ-DOC-009,REQ-DOC-011,REQ-DOC-015,REQ-DOC-024,REQ-DOC-025,REQ-DOC-026,REQ-DOC-028,REQ-DOC-029,REQ-DOC-041,REQ-DOC-042,REQ-DOC-049,REQ-DOC-050 -->
### SUB PORTS-DOCUMENT
- `HostFilePort` (port) → `GuardedFileSystemAdapter` (adapter, `path` mode). Steps per location, in order (AIAS-5):
  1. Empty location → UNREADABLE / NOT_FOUND (REQ-DOC-007).
  2. No `aias.documents.storage-root` → UNREADABLE / OUTSIDE_STORAGE_ROOT, nothing opened (REQ-DOC-011).
  3. Resolve: `root.resolve(location).normalize()`; if the file exists, `toRealPath()` (follows symbolic links) (REQ-DOC-008). A location that does not start with `root.toRealPath()` after resolution → UNREADABLE / OUTSIDE_STORAGE_ROOT, never opened (REQ-DOC-009). An absolute location is resolved the same way and so fails unless it lies inside the root.
  4. Missing file → UNREADABLE / NOT_FOUND (REQ-DOC-007).
  5. `Files.size()` before any read (REQ-DOC-041); larger than `aias.check.max-file-size` → UNREADABLE / TOO_LARGE, no byte read (REQ-DOC-042).
  6. Open with `StandardOpenOption.READ` only (REQ-DOC-049). The adapter has no write, move, rename or delete method at all (REQ-DOC-050).
- `BlobContent` (from `JdbcDocumentSourceAdapter`) — null or zero-length column → UNREADABLE / NOT_FOUND (REQ-DOC-015); length above the maximum file size → UNREADABLE / TOO_LARGE, content not streamed (REQ-DOC-041, REQ-DOC-042).
- `FormatDetector` (domain service) — detects the format from the content signature, never the file name (REQ-DOC-024): `%PDF` → PDF; OLE2 compound file with a Workbook stream → `.xls`; ZIP with `xl/workbook.xml` → `.xlsx`; JPEG `FF D8 FF`, PNG `89 50 4E 47`, TIFF `II*\0` / `MM\0*` → image; anything else → UNREADABLE / UNSUPPORTED_FORMAT (REQ-DOC-028).
- `DocumentReaders` (port per format, adapters replaceable):
  - `PdfTextReader` (Apache PDFBox) — text extraction; a PDF whose extracted text is blank is routed to the document-reading step as a scanned document (REQ-DOC-025, REQ-DOC-027).
  - `SpreadsheetTableReader` (Apache POI, HSSF + XSSF) — every sheet as a table of rows × columns of cell values as displayed (REQ-DOC-026).
  - A reader exception (damaged, encrypted, password-protected) → UNREADABLE / READING_FAILED with the exception message as detail (REQ-DOC-029).
- `UploadedDocumentStore` (repository adapter over DOC_UPLOADED_DOC) — save, read by CHECK_ID, count by CHECK_ID, delete by CHECK_ID, delete the rows of ended Checks; no update (RULE-DOC-004).
- `EndedCheckStore` (repository adapter over DOC_ENDED_CHECK) — exists by CHECK_ID, insert if absent; no update, no delete (ADR-DOC-015).
<!-- SUB:PORTS-DOCUMENT:END -->
