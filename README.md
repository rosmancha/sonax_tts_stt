# SONAX TTS e STT for GCP
Clients TTS e STT do Google GCP para o ambiente SONAX

Esses clients visam facilitar o uso da implementação que já existe no ambiente SONAX PABX,
para que novas aplicações que desejem fazer uso de fala natural dentro do ambiente SONAX
possam implementa-las, rapidamente, independente de linguagem de programação, tendo em vista que a comunicação com
os mesmos se dá via STDIN e STDOUT. Os clients rodam na CLI de qualquer distribuição Linux.

Existem 2 clients, uma para TTS (Text To Speech) e outro para STT (Speech To Text). Seguem instruções.

Local padrão dos clients: Servidor web root do SONAX PABX
Pasta padrão: /var/www/bin/sonax_tts_stt/
Client TTS: sonax_tts_gcp
Client STT: sonax_stt_gcp

EXEMPLOS DE USO:

PHP-----------------------------------------------------------------------------------------------
<?php
#TTS
$vconteudo = shell_exec('/pasta/local/sonax_tts_gcp "olá Ricardo, como vai ?"');
#O client está programado para retornar o audio sintetizado em formato WAV
file_put_contents("teste_tts_sonax.wav", $vconteudo); //GRAVA O CONTEUDO DO AUDIO SINTETIZADO
?>

<?php
#STT
#O ARQUIVO DE AUDIO TEM QUE ESTAR NO FORMATO WAV (PCM - 8 KHZ - MONO)
$vconteudo = shell_exec('/pasta/local/sonax_stt_gcp arquivo_de_audio.wav');
echo $vconteudo; #IMPRIME O CONTEÚDO DO AUDIO EM TEXTO
?>


NODE.JS----------------------------------------------------------------------------------------
#TTS
#! /usr/bin/env node
const fs = require('fs');
const { exec } = require('child_process');
const path = require("path");

exec(`/pasta/local/sonax_tts_gcp "olá Ricardo, como vai ?"`, (error, stdout, stderr) => {
    if (error) {
        console.log(`error: ${error.message}`);
        return
    }
    if (stderr) {
        console.log(`stderr: ${stderr}`);
    }

   fs.writeFile('teste_tts_sonax.wav', stdout, err => {
    if (err) {
      console.error(err)
      return
    }
   })//ARQUIVO DE AUDIO SINTETIZADO GRAVADO
});

#STT
#! /usr/bin/env node
const fs = require('fs');
const { exec } = require('child_process');
const path = require("path");

exec(`/pasta/local/sonax_stt_gcp arquivo_de_audio.wav`, (error, stdout, stderr) => {
    if (error) {
        console.log(`error: ${error.message}`);
        return
    }
    if (stderr) {
        console.log(`stderr: ${stderr}`);
    }

   console.log(stdout) //IMPRIME O CONTEÚDO DO AUDIO EM TEXTO
});


SHELL--------------------------------------------------------------------------------------
#TTS
/pasta/local/sonax_tts_gcp "olá Ricardo, como vai ?" > teste_tts_sonax.wav

#STT
/pasta/local/sonax_stt_gcp arquivo_de_audio.wav
