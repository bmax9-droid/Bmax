
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>bmax - Image Sharing</title>
  <!-- Supabase JS Library -->
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
</head>
<body>
  <h1>bmax</h1>

  <div id="upload-section">
    <input type="file" id="imageInput" accept="image/*" />
    <input type="text" id="captionInput" placeholder="Write a caption..." />
    <button id="uploadBtn">Upload Picture</button>
    <p id="statusMessage"></p>
  </div>

  <script>
    const SUPABASE_URL = "YOUR_SUPABASE_URL";
    const SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_KEY";
    const supabase = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    document.getElementById('uploadBtn').addEventListener('click', handleUpload);

    async function handleUpload() {
      const fileInput = document.getElementById('imageInput');
      const captionInput = document.getElementById('captionInput');
      const status = document.getElementById('statusMessage');
      const file = fileInput.files[0];

      if (!file) {
        status.innerText = "Please select an image first.";
        return;
      }

      // Check user session
      const { data: { user } } = await supabase.auth.getUser();
      if (!user) {
        status.innerText = "Please log in to upload pictures.";
        return;
      }

      status.innerText = "Uploading picture...";

      try {
        // Generate unique filename to prevent overwrites
        const fileExt = file.name.split('.').pop();
        const fileName = `${user.id}/${Date.now()}.${fileExt}`;

        // 1. Upload to Supabase Storage
        const { data: storageData, error: storageError } = await supabase.storage
          .from('bmax-images')
          .upload(fileName, file, {
            cacheControl: '3600',
            upsert: false
          });

        if (storageError) throw storageError;

        // Get public URL
        const { data: publicUrlData } = supabase.storage
          .from('bmax-images')
          .getPublicUrl(fileName);

        // 2. Insert record into database
        const { error: dbError } = await supabase
          .from('posts')
          .insert([
            {
              user_id: user.id,
              image_url: publicUrlData.publicUrl,
              caption: captionInput.value
            }
          ]);

        if (dbError) throw dbError;

        status.innerText = "Picture uploaded successfully!";
        fileInput.value = "";
        captionInput.value = "";

      } catch (err) {
        status.innerText = "Upload failed: " + err.message;
        console.error(err);
      }
    }
  </script>
</body>
</html>
