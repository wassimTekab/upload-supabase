<template>
  
  <h5>Upload simple</h5>
 
  <img
    :src="
      avatarUrl
        ? avatarUrl
        : 'https://img-19.ccm2.net/WNCe54PoGxObY8PCXUxMGQ0Gwss=/480x270/smart/d8c10e7fd21a485c909a5b4c5d99e611/ccmcms-commentcamarche/20456790.jpg'
    "
    alt="Avatar"
    class="avatar image"
    style="height: 80px, width: 80px"
  />
  <div style="width: 150px">
    <input
      type="file"
      id="single"
      accept="image/*"
      @change="uploadAvatar"
      :disabled="uploading"
    />
    <label class="button primary block" htmlFor="single">
      <span>{{ uploading ? "UpLoading..." : "Upload" }}</span>
    </label>
  </div>
</template>

<script>
import { ref } from "@vue/runtime-core";
import { supabase } from "../supabaseClient";


export default {
  name: "App",
  setup() {
    const avatarUrl = ref(null);
    const uploading = ref(false);

    // watch(
    //   () => avatarUrl.value,
    //   (cur) => {
    //     downloadImage(cur);
    //   }
    // );

    /**
     *
     */
    const downloadImage = async (path) => {
    //   console.log(avatarUrl.value);
      console.log("download path", path);

      if (!path) {
        avatarUrl.value = "./assets/logo.png";
        return;
      }
      /***************get url using createObjectURL ***************/
      // const { data, error } = await supabase.storage
      //   .from("avatars")
      //   .download(path);
      // if (error) throw error;
      // console.log(data)
      // avatarUrl.value = URL.createObjectURL(data);
      // console.log("avatarUrl",avatarUrl.value)
       /*************** get url using getPublicUrl from supabase********** */
        try {
          const { publicURL, err } =await supabase.storage
        .from("avatars")
        .getPublicUrl(path);
        if (err) throw err;
        console.log("url",publicURL)
         avatarUrl.value = publicURL
        } catch (error) {
          console.error(error.message)
        }
    };

    async function uploadAvatar(event) {
      try {
        uploading.value = true;

        if (!event.target.files || event.target.files.length === 0) {
          throw new Error("You must select an image to upload.");
        }

        const file = event.target.files[0];
        const fileExt = file.name.split(".").pop();
        const fileName = `${Math.random()}.${fileExt}`;
        const filePath = `${fileName}`;

        let { error: uploadError } = await supabase.storage
          .from("avatars")
          .upload(filePath, file);

        avatarUrl.value = filePath;

        if (uploadError) {
          throw uploadError;
        }
    
      } catch (error) {
        alert(error.message);
      } finally {
        uploading.value = false;
        console.log(avatarUrl.value);
        downloadImage(avatarUrl.value)
      }
    }

    // downloadImage(avatarUrl.value)

    return {
      avatarUrl,
      uploading,
      uploadAvatar,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
