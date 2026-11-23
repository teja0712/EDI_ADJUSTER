# EDI Amount Adjuster — Cloudflare Pages

This is a static browser application. No server, database, Node.js installation, or build process is required.

## Included files

- `index.html` — the complete EDI Amount Adjuster application
- `_headers` — basic browser security headers for Cloudflare Pages
- `README.md` — these deployment instructions

## Deploy through GitHub and Cloudflare Pages

### 1. Create a GitHub repository

1. Sign in to GitHub.
2. Create a new repository, for example `edi-amount-adjuster`.
3. Extract this ZIP on your computer.
4. Upload **all files inside the extracted folder** to the top/root level of the repository.
5. Confirm that `index.html` is visible directly in the repository root, not inside another nested folder.
6. Commit the files to the `main` branch.

Expected repository layout:

```text
edi-amount-adjuster/
├── index.html
├── _headers
└── README.md
```

### 2. Connect the repository to Cloudflare Pages

1. Sign in to the Cloudflare dashboard.
2. Open **Workers & Pages**.
3. Choose **Create application** → **Pages** → **Connect to Git**.
4. Authorize GitHub if prompted.
5. Select the `edi-amount-adjuster` repository.

### 3. Set the build configuration

Use these settings:

| Setting | Value |
|---|---|
| Production branch | `main` |
| Framework preset | `None` |
| Build command | Leave blank |
| Build output directory | `.` |
| Root directory | `/` or leave default |

Click **Save and Deploy**.

### 4. Open the deployed application

Cloudflare will provide a URL similar to:

`https://your-project-name.pages.dev`

Open it and test with a non-production EDI file first.

## Updating the application

When you change `index.html` or another project file:

1. Commit and push the changes to the connected GitHub branch.
2. Cloudflare Pages will automatically start a new deployment.
3. Refresh the Pages URL after deployment completes.

## Validation coverage

- Supports professional 837P service-line billed amounts from SV102.
- Supports institutional 837I service-line billed amounts from SV203.
- Groups LX service lines under their own CLM claim; amounts from separate claims are never combined.
- Displays claim-level CLM02 versus the sum of that claim's LX billed amounts.
- Displays claim-level HCP02 versus the sum of that claim's LX HCP02 values when present.
- Continues to audit each LX independently using its billed amount, SVD02 total, and CAS adjustment total.
- Claim-level mismatches are flagged for review; the tool does not automatically rewrite CLM02 or SV102/SV203.
- User-triggered adjustments target only the last CO*45 amount in the last CAS segment containing CO*45 within each LX service line. Other CAS groups/reason codes and amounts are preserved.
- If the proposed CO*45 amount would become negative, the row is highlighted for manual review and that LX adjustment is skipped.
- If creating a CAS segment is enabled for a line with no CAS segment, only a CO*45 segment may be created; negative proposed amounts are flagged and not created.
- Validates every ST/SE transaction pair: SE01 against the actual segment count from ST through SE (inclusive), and ST02 against SE02.
- Reports missing ST or SE segments and invalid/mismatched SE01 counts without automatically changing transaction structure.

## Important data-handling notes

- The application reads and processes the selected EDI file in the browser.
- The application itself does not upload the selected EDI file to Cloudflare.
- Keep an untouched copy of every original EDI file.
- Validate adjusted files and payer-specific CAS rules with your EDI team before production use.
- This tool is not an X12 compliance validator.
