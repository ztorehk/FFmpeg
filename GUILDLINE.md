###########################################
################## Build ##################
###########################################

### Ubuntu

cd /tmp
wget https://www.nasm.us/pub/nasm/releasebuilds/2.16.03/nasm-2.16.03.tar.xz
tar xf nasm-2.16.03.tar.xz
cd nasm-2.16.03
./configure --prefix=/usr/local
make -j$(nproc)
sudo make install
sudo apt install -y libssl-dev

cd /home/tai/git-repo/FFmpeg
make clean
./configure \
  --prefix=/usr/local \
  --enable-gpl --enable-nonfree \
  --enable-libx264 --enable-libx265 --enable-libvpx \
  --enable-libfdk-aac --enable-libmp3lame --enable-libopus \
  --enable-libass --enable-libfreetype \
  --enable-openssl \
  --x86asmexe=/usr/local/bin/nasm
make -j$(nproc)


###########################################
################## Usage ##################
###########################################

ffmpeg -i in.mov -c:v copy -c:a copy -f mxf \
  -mxf_company_name "Adobe Inc." \
  -mxf_product_name "Adobe Media Encoder" \
  -mxf_product_version "25.4.1" \
  -mxf_version_string "2.0.0.0.1" \
  -mxf_platform "win32" \
  -mxf_toolkit_version "1.1.12.0.1" \
  -mxf_product_uid "0c3919fe-46e8-11e5-a151-feff819cdc9f" \
  -mxf_identification_uid "3f1c7e97-afec-11f0-84a9-08bfb8b98957" \
  -mxf_generation_uid     "3f1c7e98-afec-11f0-8125-08bfb8b98957" \
  -mxf_material_umid      "060A2B340101010501010D1113000000139ADE0171090698405E08BFB8B98957" \
  -mxf_adobe_track_ids 1 \
  -mxf_instance_number 0 \
  out.mxf

說明：
- mxf_company_name：覆寫 Company Name (3C01)
- mxf_identification_uid：覆寫 Identification UID (3C0A) 16-byte hex
- mxf_generation_uid：覆寫 Generation UID (3C09) 16-byte hex
- mxf_product_uid：覆寫 Product UID (3C05) 16-byte hex
- mxf_material_umid：覆寫 Material Package UMID，需 32-byte hex（完整 UMID 寫入 0x4401）
- mxf_adobe_track_ids：使用 Adobe 風格 Track ID（Video=512，Audio=768/1024/1280/1536…）
- mxf_instance_number：覆寫 UMID 中的 instance number（24 bits），影響自動產生的 UMID/Linked Package UID；若已用 mxf_material_umid 完全覆寫 Material UMID，該值只影響其他自動生成的 UMID。