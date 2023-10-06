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


SINTAXE:
TTS - /pasta/local/sonax_tts_gcp "<TEXTO_A_SINTETIZAR>" [CODIGO_IDIOMA*] [GÊNERO**] [NOME_VOZ***]
STT - /pasta/local/sonax_stt_gcp arquivo_de_audio.wav

* Lista de código de idiomas no padrão da RFC BCP-47. Se nada for informado, pt-BR será usado (https://cloud.google.com/text-to-speech/docs/reference/rest/v1/voices/list).
** Pode ser 'F' para Feminino ou 'M' para Masculino. Se nada for informado, 'F' será usado.
*** Código de nome de possíveis vozes. Se nada for informado, pt-BR-Neural2-A será usado para feminino e pt-BR-Neural2-B será usado para masculino (https://cloud.google.com/text-to-speech/docs/reference/rest/v1/voices/list).


PRÉ-REQUISITOS:
1 - É necessário chave de acesso SSH cadastrada no servidor TTS/STT da SONA a partir do servidor ou estação de origem
    1.1 - A maioria dos servidores SONAX tem chaves de acesso SSH cadastrada no servidor de TTS/STT. 
    Caso você precise cadastrar sua chave de acesso SSH contate o administrador de sistemas.
2 - O formato do audio para envio tem que ser WAV, no perfil PCM (8 KHZ de amostragem e monocanal).


EXEMPLOS DE USO:

PHP-----------------------------------------------------------------------------------------------
<?php
#TTS
$vconteudo = shell_exec('/pasta/local/sonax_tts_gcp "olá Ricardo, como vai ?" pt-BR F pt-BR-Neural2-A');
#O CLIENT ESTÁ PROGRAMADO PARA DEVOLVER O AUDIO NO FORMATO WAV
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

exec(`/pasta/local/sonax_tts_gcp "olá Ricardo, como vai ?" pt-BR F pt-BR-Neural2-A`, (error, stdout, stderr) => {
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
   })//GRAVA O ARQUIVO DE AUDIO SINTETIZADO
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
/pasta/local/sonax_tts_gcp "olá Ricardo, como vai ?" pt-BR F pt-BR-Neural2-A > teste_tts_sonax.wav

#STT
/pasta/local/sonax_stt_gcp arquivo_de_audio.wav
