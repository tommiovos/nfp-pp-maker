<script>
    import { onMount } from "svelte";
    import { browser } from '$app/environment'; 
    let uploadedImage;
    let overlayImage;
    let canvas;
    let ctx;
    let imageUpload;
    let downloadBtn;
    const desiredWidth = 500; // Desired width of the canvas
    const desiredHeight = 500; // Desired height of the canvas

    onMount(() => {
        if (browser) {
            canvas = document.getElementById('canvas');
            ctx = canvas.getContext('2d');
            imageUpload = document.getElementById('image-upload');
            downloadBtn = document.getElementById('download-btn');

            setOverlayImage('overlay-2.png');

            imageUpload.addEventListener('change', () => {
                redrawPP();
            });

            downloadBtn.addEventListener('click', () => {
                if (uploadedImage) {
                    const link = document.createElement('a');
                    link.download = 'pp-nouveau-font-populaire.png';
                    link.href = uploadedImage;
                    link.click();
                }
            });
        }
        
    });

    function changeImg(img) {
            // Calculate the aspect ratio of the uploaded image
            const aspectRatio = img.width / img.height;

            let width, height, x, y;

            if (aspectRatio > 1) {
                width = desiredWidth;
                height = desiredWidth / aspectRatio;
                if (height < desiredHeight) {
                    height = desiredHeight;
                    width = desiredHeight * aspectRatio;
                }
            } else {
                height = desiredHeight;
                width = desiredHeight * aspectRatio;
                if (width < desiredWidth) {
                    width = desiredWidth;
                    height = desiredWidth / aspectRatio;
                }
            }

            x = (desiredWidth - width) / 2;
            y = (desiredHeight - height) / 2;

            // Set canvas dimensions to the desired size
            canvas.width = desiredWidth;
            canvas.height = desiredHeight;

            // Draw the uploaded image onto the canvas, preserving aspect ratio and centering
            ctx.drawImage(img, 0, 0, img.width, img.height, x, y, width, height);

            // Draw the overlay image on top of the resized image
            drawOverlayImage();

            return canvas.toDataURL('image/png');
        }

    function clearCanvas() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    function setOverlayImage(src) {
        overlayImage = new Image();
        overlayImage.src = src;
        overlayImage.onload = () => {
            drawOverlayImage(true);
            if (uploadedImage != null) {
                redrawPP();
            }
        };
    }

    function redrawPP() {
        const file = imageUpload.files[0];
        const reader = new FileReader();

        reader.onload = () => {
            const img = new Image();
            img.onload = () => { uploadedImage = changeImg(img) };
            img.src = reader.result;
        };

        reader.readAsDataURL(file);
        downloadBtn.classList.add("active");
    }

    function drawOverlayImage(clear=false) {
        if (clear) {
            clearCanvas();
        }
        ctx.drawImage(overlayImage, 0, 0, desiredWidth, desiredHeight);
    }

    
</script>


<section class="main-grid">
    <span class="logo-top-bg"></span>

    <h1>Créateur de PP Nouveau Front Populaire</h1>
    <canvas id="canvas" width="500" height="500"></canvas>

    <div class="options-cont">
        <p>Style de PP</p>
        <div class="options">
            <label for="overlay-classique" on:click={() => {setOverlayImage('overlay-2.png')}}>Cercle
                <input type="radio" name="overlay" id="overlay-classique" checked>        
            </label>

            <label for="overlay-bandes" on:click={() => {setOverlayImage('overlay-bandes.png')}}>Bandes
                <input type="radio" name="overlay" id="overlay-bandes">    
            </label>        
        </div>    
    </div>
    
    
    <div class="btns">
        <label for="image-upload" class="upload">
            Importer une image
            <input type="file" id="image-upload" accept="image/*">    
        </label>
        <button class="{uploadedImage == null ? '' : 'active'}" id="download-btn">Télécharger la PP</button>     
    </div>
    
</section>




<style>
    @import url('https://fonts.googleapis.com/css2?family=Radio+Canada+Big:ital,wght@0,400..700;1,400..700&display=swap');

    *, *::before, *::after {
        box-sizing: border-box;
    }

    :global(body) {
        margin: 0;
        height: 100%;
        width: 100%;
    }

    h1 {
        font-size: 2rem;
        font-family: "Radio Canada Big", sans-serif;
        grid-area: title;
        text-align: center;
        width: 100%;
        padding-top: 2rem;
    }

    canvas {
        grid-area: preview;
        width: 100%;
        border-radius: 50rem;
        background-color: rgb(197, 197, 197);
        box-shadow: -33px 117px 49px rgba(0, 0, 0, 0.01), -19px 66px 41px rgba(0, 0, 0, 0.05), -8px 29px 30px rgba(0, 0, 0, 0.09), -2px 7px 17px rgba(0, 0, 0, 0.1);
    }

    .options-cont {
        grid-area: options;  
        display: flex;
        flex-direction: column;
        justify-content: center;  
        align-items: center;
    }
    
    .options-cont > p {
        font-size: 1.25rem;
    }

    .options-cont * {
        font-family: "Radio Canada Big", sans-serif;
    }

    .options {
        display: flex;
        gap: 1rem;
    }

    .options > * {
        color: white;
        padding: 1rem;
        background-color: #f5759d;
        border: 2px solid #E70E53;
        border-radius: 1rem;
        width: 8rem;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 1.1rem;
    }

    .btns {
        grid-area: btns;
        width: 100%;
        display: flex;
        gap: 1rem;
        align-items: center;
        justify-content: center;
    }

    .btns > * {
        font-size: 1rem;
        text-align: center;
    }
    
    .upload {
        cursor: pointer;
        grid-area: upload;
        padding: 1.2rem 3rem;
        font-family: "Radio Canada Big", sans-serif;
        background-color: #E70E53;
        color: white;
        border-radius: 0.5rem;
    }

    .upload input {
        display: none;
        padding: 1rem;
    }

    button {
        grid-area: download;
        padding: 1.2rem 3rem;
        font-family: "Radio Canada Big", sans-serif;
        background-color: #4f4f4f;
        color: white;
        border-radius: 0.5rem;
        border: 0px;
        opacity: 0.5;
        transition: background-color 0.1s ease-out;
    }

    button.active {
        background-color: #E70E53;
        opacity: 1;
    }

    .main-grid {
        padding: 2rem;
        position: relative;
        width: 100%;
        height: 100%;
        display: grid;
        grid-template-columns: 1fr auto 1fr;
        grid-template-rows: auto auto auto auto;
        grid-template-areas: 
            ". title ."
            ". preview ."
            ". options ."
            ". btns ."
            ;
        gap: 2rem;
    }

    .main-grid > * {
        place-self: center;
    }

    .logo-top-bg {
        width: 100%;
        height: 30px;
        background-image: url(/tileable.jpg);
        background-repeat: repeat-x;
        background-size: 460px 32px;
        position: absolute;
        top: 0;
        left: 0;
        animation: move-background 6s linear infinite;
    }

    @keyframes move-background {
        0% {
            background-position: 0px 0px;
        }
        100% {
            background-position: 460px 0px;
        }
    }

    @keyframes move-background-second {
        0% {
            transform: translateX(-100%);
        }
        100% {
            transform: translateX(0%);
        }
    }

    @media screen and (max-width: 1400px) and (orientation: landscape) {
        canvas {
            max-width: 30vw;
        }
    }

    @media screen and (min-width:800px) and (orientation: portrait) {
        .main-grid {
            grid-template-columns: 15vw auto 15vw;
        }
        canvas {
            max-height: 40vh;
            max-width: 40vh;
        }
    }

    @media screen and (max-width: 800px) {
        .btns {
            flex-direction: column;
        }

        h1 {
            font-size: 1.75rem;
        }
    }
</style>
