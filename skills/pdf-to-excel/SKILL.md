---
name: pdf-to-excel
description: Convert one or more PDFs (bank statement, credit card statement, invoice, receipt, report, price list, any table) into Excel .xlsx workbooks with Better Analyst, one workbook per PDF. Use whenever the user asks to turn PDFs into Excel, a spreadsheet, rows, or a table they can download.
---

# PDF to Excel with Better Analyst

1. Find out where the PDFs are.
   - If the user gave public HTTPS links to PDFs, go to step 3 and pass them as `fileUrls`.
   - If the PDFs are on their device, or you only have a local path, call `create_pdf_upload_link` and give the user the link. It works for one hour and needs no sign-in, so give it only to this user. Do not try to upload the files yourself.
2. When the user says they have uploaded, use the `uploadId` from that call. If the tool reports that nothing has been uploaded yet, ask them to finish and try again.
3. Work out what the user wants extracted:
   - If they named columns ("date, description, amount"), pass them as `extractFields` in that order.
   - Put any other guidance ("one row per line item", "skip subtotals", "only the second table") in `instructions`.
   - If they only want some pages, pass `pageRange` such as `"2-5"`. It applies to every file.
   - Otherwise omit these and AI Extract picks sensible columns.
   - `extractFields` and `instructions` apply to every file. If different PDFs need different columns, make one call per group of PDFs that share the same settings.
4. Call `convert_pdf_to_excel` once with either `uploadId` or `fileUrls`, never both. Each page uses 10 of the user's Better Analyst AI credits. Call `get_credit_balance` first if they ask what it will cost.
5. While any file is `processing`, tell the user which are still running, then call `get_conversion_status` with the `jobIds` the result names, until each is `completed` or `failed`. Each status call waits up to about 15 seconds. Large PDFs can take a few minutes.
6. For each completed file, give its download link and say how many rows were extracted. Show the preview rows as a short markdown table: at most 10 rows for a single file, at most 5 rows per file when there are several. Download links last up to an hour. Call `get_conversion_status` again for fresh ones.
7. Tell the user about any file that failed or was skipped, and why.
8. If the user runs out of credits, say how many the conversion needs and how many they have, and give the subscribe, upgrade, or contact link from the tool result. Suggest a smaller `pageRange` when that would fit their balance. Do not retry until they have more credits.

Do not invent rows or values from the PDF yourself. The workbooks from the tool are the source of truth and the finished deliverables: do not download, rebuild, reformat, merge, or re-verify them with code or other tools. Share the links and previews, and stop.
