# ImageResizerToolForExams
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Image Resize & Extract Tool</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 0;
            padding: 20px;
            background-color: #f9f9f9;
        }
        input, button {
            margin: 10px;
            padding: 10px;
            font-size: 16px;
        }
        canvas {
            margin-top: 20px;
            border: 1px solid #ccc;
        }
        #downloadLink {
            display: none;
            margin-top: 20px;
            font-size: 16px;
            color: blue;
            text-decoration: underline;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>Image Resize & Extract Tool</h1>
    <p>Upload your image and specify the desired dimensions and file size:</p>
    <input type="file" id="imageInput" accept="image/*">
    <br>
    <label for="width">Width (px):</label>
    <input type="number" id="width" placeholder="Width in px">
    <label for="height">Height (px):</label>
    <input type="number" id="height" placeholder="Height in px">
    <br>
    <label for="size">File Size (KB):</label>
    <input type="number" id="size" placeholder="File size in KB">
    <br>
    <button onclick="resizeAndExtract()">Resize & Extract</button>
    <br>
    <canvas id="canvas"></canvas>
    <br>
    <a id="downloadLink">Download Resized Image</a>

    <script>
        function resizeAndExtract() {
            const fileInput = document.getElementById("imageInput");
            const widthInput = document.getElementById("width");
            const heightInput = document.getElementById("height");
            const sizeInput = document.getElementById("size");
            const canvas = document.getElementById("canvas");
            const ctx = canvas.getContext("2d");
            const downloadLink = document.getElementById("downloadLink");

            if (!fileInput.files[0]) {
                alert("Please select an image file.");
                return;
            }

            const img = new Image();
            const reader = new FileReader();

            reader.onload = function (e) {
                img.src = e.target.result;
            };

            img.onload = function () {
                const width = parseInt(widthInput.value) || img.width;
                const height = parseInt(heightInput.value) || img.height;
                const maxSize = parseInt(sizeInput.value) || null;

                canvas.width = width;
                canvas.height = height;

                ctx.drawImage(img, 0, 0, width, height);

                let quality = 1.0; // Start with full quality
                let dataUrl = canvas.toDataURL("image/jpeg", quality);

                // If maxSize is specified, compress the image until it meets the size
                if (maxSize) {
                    while (dataUrl.length / 1024 > maxSize && quality > 0.1) {
                        quality -= 0.05;
                        dataUrl = canvas.toDataURL("image/jpeg", quality);
                    }

                    if (dataUrl.length / 1024 > maxSize) {
                        alert("The file size cannot be reduced to the specified limit.");
                        return;
                    }
                }

                downloadLink.href = dataUrl;
                downloadLink.download = "resized-image.jpg";
                downloadLink.style.display = "block";
                downloadLink.textContent = `Download Resized Image (${Math.round(dataUrl.length / 1024)} KB)`;
            };

            reader.readAsDataURL(fileInput.files[0]);
        }
    </script>
</body>
</html>
