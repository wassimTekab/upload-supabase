<template>
  <div>
    <form @submit.prevent="onUpload">
      <div class="form-group">
        <input type="file" name="imagesArray" multiple @change="onChange" />
      </div>
      <div class="form-group">
        <button class="btn btn-success">Store</button>
      </div>
    </form>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabaseClient";
export default {
  name: "multiple",
  setup() {
    const filesArray = ref([]);
    const filesUrls = ref([]);
    const uploading = ref(false);

    const onChange = (event) => {
      filesArray.value = [...filesArray.value, ...event.target.files];


      console.log(event.target.files);
      console.log(filesArray.value)
    };

    const onUpload = async () => {
      uploading.value = true;
      //  if (!event.target.files || event.target.files.length === 0) {
      //   throw new Error("You must select an image to upload.");
      // }

      const filesExt = filesArray.value.map((file) =>
        file.name.split(".").pop()
      );
      console.log({ filesExt });
      const filesName = filesExt.map(
        (fileExt) => `${Math.random()}.${fileExt}`
      );

      console.log(filesName);
      const filesPath = filesName.map((fileName) => `files/${fileName}`);
      console.log(filesPath);
      filesArray.value.forEach(async (file, index) => {
        try {
          let { error: uploadError } = await supabase.storage
            .from("avatars")
            .upload(filesPath[index], file);
          console.log("file n", index);
          if (uploadError) {
            throw uploadError;
          }
          await downloadFiles(filesPath);
        } catch (error) {
          alert(error.message);
        }
      });
      uploading.value = false;
      
    };

    const downloadFiles = async (filesPath) => {
      // Todo  find a way to create blob object 
      //   const { data, error } = await supabase.storage
      //     .from("avatars")
      //     .list("files", {
      //       limit: 100,
      //       offset: 0,
      //       sortBy: { column: "name", order: "asc" },
      //     });
      //   if (error) {
      //     throw error;
      //   }

      //   console.log("files", data);
      //   filesUrls.value = data.map(
      //     (file) =>
      //       new Blob([JSON.stringify(file.metadata, null, 2)], {
      //         type: file.metadata.mimeType,
      //       })
      //   );
      //   console.log("filesUrls", filesUrls.value);

      /***************get url using createObjectURL ***************/
      console.log("filesPath",filesPath)
      filesPath.forEach(async (path,index) => {
        const { data, error } = await supabase.storage
          .from("avatars")
          .download(path);
        if (error) throw error;
        console.log(data);
        //  filesUrls.value = [...filesUrls.value,URL.createObjectURL(data)]
         filesUrls.value[index] = URL.createObjectURL(data)
         console.log("filesUrls", filesUrls.value);
      });
       /*************** get url using getPublicUrl from supabase********** */
      //  filesPath.forEach(async (path,index) => {
      //  try {
      //    const { publicURL, error } =await supabase.storage
      //   .from("avatars")
      //   .getPublicUrl(path);
      //   if (error) throw error;
      //    filesUrls.value[index] =publicURL
      //    console.log("filesUrls", filesUrls.value);
      //  } catch (error) {
      //    console.error(error.message)
      //  }
      // });

    };

    

    console.log("filesUrls", filesUrls.value);

    return {
      onChange,
      onUpload,
      downloadFiles,
    };
  },
};
</script>

<style scoped lang="css">
.container {
  max-width: 550px;
}
</style>
