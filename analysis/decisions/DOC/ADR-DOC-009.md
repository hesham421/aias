# ADR-DOC-009 — The free-tier rule is enforced from two settings: the document-reading model's tier and the environment's data class
Status      : ACCEPTED
Stage       : P1        Module: DOC        Version: v1
Context     : POL-DOC-016 (G13, D3) allows a free-tier document-reading model to receive only synthetic or anonymised documents. The service cannot tell from a document whether it is anonymised, so the policy needs a declared fact to be testable.
Decision    : The document-reading model configuration declares its tier, FREE or APPROVED (default FREE when not declared); the environment's document access setting declares its data class, SYNTHETIC or REAL (default REAL when not declared). While the tier is FREE and the data class is REAL, DOC sends no document to the document-reading model, and every document that needs the document-reading step is reported UNREADABLE with reason MODEL_NOT_PERMITTED. Test environments that hold only synthetic or anonymised requests declare SYNTHETIC. Text extraction and table extraction run in the service and are not affected.
Alternatives rejected: trusting operators to remember the rule — not verifiable; blocking start-up — would also stop PDF and spreadsheet reading, which sends nothing to a provider.
Consequences: The go-live provider decision (D3) switches the tier to APPROVED by configuration alone. Both settings are configuration, not entities.
traces      : US-DOC-013, POL-DOC-016, REQ-DOC-058, REQ-DOC-059
