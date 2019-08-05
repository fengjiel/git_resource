<template>
    <div>
        <div class="keep-out-btn"
             v-if="src"></div>
        <iframe class="iframe-wrap"
                v-if="src"
                :src="src"
                frameborder="0"></iframe>
        <video :src="multimediaSrc"
               v-if="multimediaSrc"
               controls
               autoplay></video>
        <img :src="imgSrc"
             v-if="imgSrc"
             alt="">
    </div>
</template>
<style lang="scss" scoped>
.keep-out-btn {
    width: 100%;
    height: 90px;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 999;
    background: #393a3d;
}
.iframe-wrap {
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
}
video::-internal-media-controls-download-button {
    display: none;
}
video::-webkit-media-controls-enclosure {
    overflow: hidden;
}
video::-webkit-media-controls-panel {
    width: calc(100% + 30px);
}
</style>
<script>
import PDFObject from "@/assets/libs/pdfobject.js";
export default {
    data() {
        return {
            src: "",
            multimediaSrc: "",
            imgSrc: ""
        };
    },
    metaInfo: {
        title: "预览文档"
    },
    created() {
        let src;
        try {
            src = this.$Base64.decode(this.$route.query.s);
        } catch (e) {
            src = this.$route.query.s;
        }
        let format = src
            .split(".")
            .pop()
            .toLocaleUpperCase();
        console.log(src);
        if (
            format == "DOC" ||
            format == "DOT" ||
            format == "DOCX" ||
            format == "XLS" ||
            format == "XLSX" ||
            format == "PPT" ||
            format == "PPTX"
        ) {
            this.src =
                `https://view.officeapps.live.com/op/view.aspx?src=` + src;
        } else if (format == "PDF") {
            if (PDFObject.supportsPDFs) {
                PDFObject.embed(src);
            } else {
                this.$message.error("您的浏览器暂不支持预览pdf格式类型文件！");
                console.log(
                    "Boo, inline PDFs are not supported by this browser"
                );
            }
        } else if (
            format == "MP3" ||
            format == "WAV" ||
            format == "AVI" ||
            format == "MP4" ||
            format == "WMV"
        ) {
            this.multimediaSrc = src;
        } else if (
            format == "BMP" ||
            format == "JPG" ||
            format == "JPEG" ||
            format == "PNG" ||
            format == "GIF"
        ) {
            this.imgSrc = src;
        } else {
            window.location.href = src;
        }
    }
};
</script>
