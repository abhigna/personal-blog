---
title: Resume
date: 2024-12-16 05:29:32
---

<!-- PDF.js CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>

<div class="pdf-container">
    <canvas id="pdf-render"></canvas>
</div>

<div class="controls">
    <button id="prev">Previous</button>
    <span id="page-num"></span> / <span id="page-count"></span>
    <button id="next">Next</button>
</div>

<script>
// Configure PDF.js worker
pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';

// PDF loading and rendering logic
const loadPDF = async () => {
    const pdfDoc = await pdfjsLib.getDocument('../resume_pdf/Resume_Abhigna_Nagaraja.pdf').promise;
    const pageCount = pdfDoc.numPages;
    document.getElementById('page-count').textContent = pageCount;
    
    let currentPage = 1;
    const canvas = document.getElementById('pdf-render');
    const ctx = canvas.getContext('2d');

    const renderPage = async (pageNum) => {
        const page = await pdfDoc.getPage(pageNum);
        const viewport = page.getViewport({ scale: 1.5 });
        
        canvas.height = viewport.height;
        canvas.width = viewport.width;

        await page.render({
            canvasContext: ctx,
            viewport: viewport
        }).promise;

        document.getElementById('page-num').textContent = pageNum;
    };

    // Initial render
    renderPage(currentPage);

    // Button handlers
    document.getElementById('prev').addEventListener('click', () => {
        if (currentPage > 1) {
            currentPage--;
            renderPage(currentPage);
        }
    });

    document.getElementById('next').addEventListener('click', () => {
        if (currentPage < pageCount) {
            currentPage++;
            renderPage(currentPage);
        }
    });
};

// Load the PDF when the page loads
window.onload = loadPDF;
</script>

<style>
.pdf-container {
    max-width: 100%;
    margin: 0 auto;
    overflow-x: auto;
}

canvas {
    max-width: 100%;
    height: auto;
}

.controls {
    text-align: center;
    margin: 1em 0;
}

button {
    padding: 0.5em 1em;
    margin: 0 0.5em;
}
</style>

