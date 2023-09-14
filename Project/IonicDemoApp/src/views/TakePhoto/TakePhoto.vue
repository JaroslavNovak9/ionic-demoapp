<template>
    <div class="take-photo-container">
        {{ platofrm }}
        <div 
            v-if="isImageSourceEmpty == false"
            class="taken-photo-container"
        >
            <ion-img v-bind:src="imageData.source"></ion-img>
            <span>Image path: {{ imageData.source }}</span>
        </div>
        <ion-button 
            v-else
            @click="openPhotoOptions"
        >Take a photo</ion-button>
    </div>
</template>

<script setup lang="ts">
// Importing some Vue specific code
import { reactive, computed, onMounted } from 'vue';
// Importing Ionic components
import { IonButton, IonImg, isPlatform, getPlatforms } from '@ionic/vue';
// Importing capacitor native plugins
import { Camera, CameraResultType, CameraSource, Photo } from "@capacitor/camera";
import { Filesystem, Directory } from '@capacitor/filesystem';
import { Preferences } from "@capacitor/preferences";
// Importing defined key for preferences
import { STORAGE_KEY } from '../../main.ts';


// Values

// Holds information about saved/taken photo to display
var imageData = reactive({
    source: "",
    description: ""
})
const isImageSourceEmpty = computed(() => {
    return imageData.source == null || imageData.source == ""
})
const platofrm = computed(() => {
    return getPlatforms()
})

// Lifecycle methods

onMounted(() => {
    console.log("Mounted")
    loadSavedPhotos()
})

// Functions

async function loadSavedPhotos() {
  // Retrieve cached photo array data
  const { value } = await Preferences.get({ key: STORAGE_KEY });
  const savedPhoto = (value ? JSON.parse(value) : null) as UserPhoto;

  if (isPlatform("hybrid")) {
    console.log(`Saved photo path => ${savedPhoto.filepath}`)
    imageData.source = savedPhoto.filepath
  } else {
    const readFile = await Filesystem.readFile({
        path: savedPhoto.filepath,
        directory: Directory.Data,
    });
    imageData.source = `data:image/jpeg;base64,${readFile.data}`;
  }
}

async function openPhotoOptions() {
    const photo = await Camera.getPhoto({
        resultType: CameraResultType.Uri,
        source: CameraSource.Prompt,
    });
    const savedPhotoFile = await saveTakenPhoto(photo);
    
    if (isPlatform("hybrid")) {
        imageData.source = savedPhotoFile.filepath;
    } else {
        imageData.source = savedPhotoFile.webviewPath;
    }
    console.log("Saved photo => ", imageData.source)

    Preferences.set({
        key: STORAGE_KEY,
        value: JSON.stringify(savedPhotoFile),
    });
}

async function saveTakenPhoto(photo: Photo) {
    const base64Data = await readAsBase64(photo);

    // Write the file to the data directory
    const fileName = Date.now() + '.jpeg';
    const savedFile = await Filesystem.writeFile({
        path: fileName,
        data: base64Data,
        directory: Directory.Data
    });

    if (isPlatform("hybrid")) {
        // Use webPath to display the new image instead of base64 since it's
        // already loaded into memory
        return {
            filepath: savedFile.uri,
            webviewPath: Capacitor.convertFileSrc(savedFile.uri),
        }
    } else {
        return {
            filepath: fileName,
            webviewPath: photo.webPath
        };
    }
}

// Help functions

async function readAsBase64(photo: Photo) {
    if (isPlatform("hybrid")) {
        const file = await Filesystem.readFile({
            path: photo.path!
        });
        return file.data
    } else {
        const response = await fetch(photo.webPath!);
        const blob = await response.blob();
        return await convertBlobToBase64(blob) as string;
    }    
}

const convertBlobToBase64 = (blob: Blob) => {
    return new Promise((resolve, reject) => {
        const reader = new FileReader();

        reader.onerror = reject;
        reader.onload = () => {
            resolve(reader.result);
        };
        reader.readAsDataURL(blob);
    })
}
</script>

<style scoped></style>