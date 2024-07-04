<template>
    <div class="container">
        <div class="data-form">
            <h1>Data Form</h1>
            <hr>
            <div class="form-block">
                <label for="idnumber">ID Number</label>
                <input type="text" id="idnumber" v-model="details.idnumber">
            </div>
            <div class="form-block">
                <label for="usernumber">User Number</label>
                <input type="text" v-model="details.usernumber">
            </div>
            <div class="form-block">
                <label for="name">Name</label>
                <input type="text" id="name" v-model="details.name">
            </div>
            <div class="form-block">
                <label for="program">Program</label>
                <input type="text" id="program" v-model="details.program">
            </div>
            <div class="form-block">
                <label for="birth_date">Birth Date</label>
                <input type="text" id="birth_date" v-model="details.birth_date">
            </div>
            <div class="form-block">
                <label for="gender">Gender</label>
                <input type="text" id="gender" v-model="details.gender">
            </div>
            <div class="form-block">
                <label for="contact_person">Contact Person</label>
                <input type="text" id="contact_person" v-model="details.contact_person">
            </div>
            <div class="form-block">
                <label for="contact_address">Contact Address</label>
                <input type="text" id="contact_address" v-model="details.contact_address">
            </div>
            <div class="form-block">
                <label for="contact_number">Contact Number</label>
                <input type="text" id="contact_number" v-model="details.contact_number">
            </div>
            <div class="form-block">
                <label for="picture">Picture</label>
                <input type="file" id="picture" ref="picture" @change="updatePic">
            </div>
            <div class="form-block">
                <label for="signature">Signature</label>
                <input type="file" id="signature" ref="signature" @change="updateSig">
            </div>

            <button class="btn" @click="generate">Generate</button>
        </div>
        <div>
            <canvas ref="canvas" id="canvas" width="915" height="682">
            </canvas>
        </div>

        <img src="../../../public/images/id_front.png" alt="ID Front" class="hidden" id="front_template">
        <img src="../../../public/images/lerin_sig.png" alt="Lerin Signature" class="hidden" id="lerin_sig">
        <div style="display: flex; flex-direction: column; gap: 1rem">
            <div class="form-block">
                <div>Signature</div>
                <img id="signature_img" alt="" style="height: 100px;">
            </div>
            <div class="form-block">
                <div>Picture</div>
                <img id="picture_img" alt="" style="height: 200px; width: 200px">
            </div>
            <div class="form-block">
                <div>QR Code</div>
                <qrcode-vue :value="details.usernumber" id="qrcode" size="200"/>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import QrcodeVue from 'qrcode.vue'

let details = ref({
    idnumber: '',
    usernumber: '',
    name: '',
    program: '',
    birth_date: '',
    gender: '',
    contact_person: '',
    contact_address: '',
    contact_number: '',
})

const sig_path = ref(null)

const canvas = ref('canvas')
const img = ref('front')

function updateSig(ev) {
    if(!ev.target.files) return;

    var file = ev.target.files[0]

    if(file) {
        document.getElementById('signature_img').src = URL.createObjectURL(file);
    }

}

function updatePic(ev) {
    if(!ev.target.files) return;

    var file = ev.target.files[0]

    if(file) {
        document.getElementById('picture_img').src = URL.createObjectURL(file)
    }
}

function generate() {
    let ctx = canvas.value.getContext("2d")

    const img = document.getElementById('front_template')
    const lerin_sig = document.getElementById('lerin_sig')

    //front
    // ctx.drawImage(img, 0, 0, 306,486)
    ctx.drawImage(img, 0, 0, 429,682)

    // let centerX = 306/2;
    let centerX = 429/2;

    ctx.fillStyle = "rgb(1 31 128 / 70%)"
    ctx.fillRect(centerX-112, 474, 224, 38)
    ctx.font = "bold 24px sans"
    ctx.fillStyle = "yellow"
    let txtLen = ctx.measureText(details.value.idnumber)
    ctx.fillText(details.value.idnumber, centerX-(txtLen.width/2), 502)
    ctx.font =  "20px arial"
    ctx.fillText(details.value.program, centerX-(ctx.measureText(details.value.program).width/2), 572)

    ctx.fillStyle = "#47fff9"

    ctx.font = "bold 19pt arial narrow"

    ctx.fillText(details.value.name, centerX - (ctx.measureText(details.value.name).width/2), 547)

    const sigImage = document.getElementById('signature_img')

    const w = aspectWidth(sigImage, 50)

    const w = aspectWidth(sigImage, 70)

    ctx.drawImage(sigImage, centerX - (w/2), 588, w, 76)

    const picImage = document.getElementById('picture_img')
    const picWidth = 236

    ctx.drawImage(picImage, centerX-(picWidth/2), 228,picWidth, picWidth)

    //back
    ctx.lineWidth = 3
    ctx.fillStyle = "#000"
    ctx.font = "20px sans"
    ctx.strokeRect(485, 14, 406, 35)
    ctx.fillText("ID Validity Indicator", 589, 38)
    ctx.strokeRect(485, 49, 406, 224)

    // ctx.fillRect(345, 205, 100,100)
    let qrcode = document.getElementById('qrcode')
    ctx.drawImage(qrcode, 345, 205, 100,100)


    ctx.fillText(details.value.birth_date, 460, 248)
    ctx.fillText(details.value.gender, 460, 287)
    ctx.fillText("President", 455, 465)

    ctx.font = "bold 14px arial"
    ctx.fillText("Birth Date:", 450, 230)
    ctx.fillText("Gender:", 450, 270)
    ctx.fillText("In case of emergency please contact:", 345, 335)
    ctx.fillText("Name: ", 345, 355)
    ctx.fillText("Address: ", 345, 375)
    ctx.fillText("Mobile No.: ", 345, 395)
    ctx.fillText("DR. MARIANO M. LERIN, CPA", 397, 448)

    ctx.font = "bold 18px arial"
    ctx.fillText("Birth Date:", 631, 322)
    ctx.fillText("Gender:", 631, 378)
    ctx.fillText("In case of emergency please contact:", 484, 470)
    ctx.fillText("Name: ", 484, 498)
    ctx.fillText("Address: ", 484, 526)
    ctx.fillText("Mobile No.: ", 484, 554)
    ctx.fillText("DR. MARIANO M. LERIN, CPA", 567, 628)

    ctx.fillText(details.value.contact_person, 430, 355)
    ctx.fillText(details.value.contact_address, 430, 375)
    ctx.fillText(details.value.contact_number, 430, 395)

    ctx.fillText(details.value.contact_person, 603, 498)
    ctx.fillText(details.value.contact_address, 603, 526)
    ctx.fillText(details.value.contact_number, 603, 554)

    ctx.drawImage(lerin_sig, 631, 544, 140, 112)
}

function aspectWidth(img, height) {
    let w = img.width
    let h = img.height;

    const aspectRatio = w/h;

    const width = (height * aspectRatio).toFixed(0);

    return width;
}

</script>

<style scoped>
.container {
    display: flex;
    gap: 2rem;
    margin-block: 2rem;
    margin-inline: auto;
}

.form-block {
    display: flex;
    flex-direction: column;
    margin-block: 4px;
    min-width: 300px;
}

.form-block input {
    padding: 4px;
    border-radius: 5px;
    border: 1px solid #b4b4b4;
}

.btn {
    padding: 12px 24px;
    background-color: #054f99;
    color: #fff;
    border: 0;
    border-radius: 8px;
    font-size: 1.2em;
}

.btn:hover {
    background-color: #136bc4;
    transition-duration: 300ms;
}

#canvas {
    border: 0px solid #b4b4b4;
}

.hidden {
    display: none;
}

</style>
