# Shrink for Upload – design notes

This file grows step by step. Each step is written down and agreed on before the next one starts.
Code comes last.

| Step | Status |
|---|---|
| 1. Problem | done |
| 2. Scope | done |
| 3. Kind of tool | – |
| 4. Technology | – |
| 5. Design | – |
| 6. Work plan | – |
| 7. Code and tests | – |
| 8. Release | – |

---

## 1. Problem

### In one sentence

*"I have to upload a private document, the website says the file is too big, and the only easy fix
is to give my document to another website I don't know."*

### Who has this problem

People who must upload documents to an online form:

- visa and immigration applications
- government services
- job applications
- university and scholarship applications

### When it happens

- The form only accepts small files. Common limits are 2 MB, 1 MB, sometimes 500 KB or even 300 KB
  per file.
- Scans and phone photos are much bigger, often several MB per page.
- Some forms accept only **one file per field**. Then several pages (scanned one by one) must first
  be put into one PDF, and that PDF must still be under the limit.

### What people do today, and why it hurts

Most people search for "compress PDF" and use the first free website. They upload their file, the
website makes it smaller, they download it again.

But these are the most private documents a person has: passport, ID card, bank statements,
certificates. Once the file is uploaded, nobody can check what happens to it. This is not only a
theory: in 2024 two online PDF converters were found leaking thousands of documents their users had
uploaded.

Some people know this and refuse to upload their passport. Then they are stuck.

### Is the problem real?

Yes. Some examples found while checking:

- The Canadian immigration website has a help page only about this question: "My documents are too
  large to upload". It says the limit can be as low as 2 MB.
  <https://ircc.canada.ca/english/helpcentre/answer.asp?qnum=1213>
- An Australian immigration guide says documents attached after a citizenship application must be
  no larger than 500 KB.
  <https://lebanon.embassy.gov.au/files/birt/ImmiAccount - tips for attaching documents to an online application - July 2017.pdf>
- A person on a computer help forum needed a PDF under 300 KB for a consulate and did not want to
  upload their passport to an online converter.
  <https://www.bleepingcomputer.com/forums/t/605917/shrinking-pdf-safely/>
- A visa applicant had scanned a 10-page form page by page, but the portal wanted one single
  document under 2 MB.
  <https://www.visajourney.com/forums/topic/680214-problems-uploading-aos-documents-to-ceac>
- Applicants for a Thai visa keep asking what to do because the portal says their files are too
  big, even after compressing.
  <https://ask.in.th/question/how-can-i-upload-documents-for-dtv-if-they-are-too-large>
- News about the leak: online PDF converters exposed thousands of user documents (2024).
  <https://bgr.com/tech/online-pdf-converters-leaked-thousands-of-user-uploaded-documents/>

### What we learned (input for step 2)

The problem has three parts:

1. **Size** – the file must get under a given limit (e.g. "under 2 MB").
2. **Many pages, one file** – several scans or photos must become one PDF.
3. **Privacy** – the document must not leave the person's own computer.

Step 2 decides which of these the first version solves.

---

## 2. Scope

The first version solves all three parts from step 1, but only in the simplest way.

### What the first version does

1. **Takes files in:** PDF files and photos (JPG, PNG) – for example scans, or pictures of a
   document taken with a phone. One file or many.
2. **Puts them in order:** the user sees every page as a small picture, can change the order and
   remove pages.
3. **Makes one PDF:** all pages go into one single PDF file.
4. **Gets under a size limit:** the user types the limit of the website (e.g. `2 MB` or `500 KB`).
   The tool finds the best quality that still fits under that limit by itself. The user does not
   have to know anything about "DPI" or "quality".
5. **Shows the result:** the new file size, and a preview so the user can check that the text is
   still readable before saving.
6. **Says it honestly when it is not possible:** if the limit is too small for the number of pages,
   the tool says so and suggests what helps (fewer pages, black and white).
7. **Black and white option:** one switch that makes the file smaller when color is not needed.
8. **Stays private:** everything happens on the user's own computer. No upload, no account, no
   ads. Works without internet.

### What the first version does NOT do (on purpose)

| Not now | Why |
|---|---|
| Keep text selectable / searchable | Simple shrinking turns each page into a picture. Fine for scans and photos, which are pictures anyway. |
| Read text from scans (OCR) | Big extra work, not needed to pass an upload limit. |
| Split a file into several parts | Most people need one file. Can come later. |
| Save as JPG instead of PDF | Can come later if people ask. |
| Straighten or crop phone photos | Nice, but a different problem. |
| Word files, password-protected PDFs | Less common for this problem. |
| Many languages | English first. The texts are kept in one place so other languages are easy to add later. |

### How we know the first version works

- 10 phone photos of a document (about 3 MB each) become **one PDF under 2 MB**, and the text is
  readable at normal zoom.
- A 1-page passport scan gets **under 300 KB** and is still readable.
- If a PDF is already under the limit, the tool says so and does not make it worse.
- It works on Windows, macOS and Linux, without internet.
