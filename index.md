<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    #recorder {
  position: relative;
  width: 3rem;
  height: 3rem;
  border-radius: 3rem;
  background: #38383d;
  border: 1px solid #f9f9fa33;
  cursor: pointer;
  box-shadow: 0 1px 4px 0 rgba(12, 12, 13, 0.2), 0 0 0 1px rgba(0, 0, 0, 0.15);
  transition: 0.2s ease;
}
#recorder #record {
  width: 60%;
  height: 60%;
  top: 20%;
  left: 20%;
  position: absolute;
  transition: inherit;
}
#recorder #arrow {
  width: 50%;
  height: 50%;
  top: 30%;
  left: 25%;
  position: absolute;
  transition: inherit;
  opacity: 0;
}
#recorder:active {
  border-color: transparent;
}
#recorder:active #record {
  width: 55%;
  height: 55%;
  top: 23%;
  left: 23%;
}
#recorder.recording {
  box-shadow: 0 0 0 1px #45a1ff, 0 0 0 4px rgba(69, 161, 255, 0.3);
}
#recorder.recording #record {
  animation: recording 0.7s ease infinite;
}
#recorder.download #record {
  height: 40%;
  width: 40%;
  top: 15%;
  left: 30%;
  animation: none;
}
#recorder.download #arrow {
  animation: download 0.5s ease infinite;
}
#recorder.out #record {
  animation: out 0.8s ease, in 0.2s 0.8s ease;
}

@keyframes in {
  from {
    height: 0%;
    top: 60%;
  }
}
@keyframes recording {
  from, to {
    transform: rotate(10deg);
  }
  50% {
    transform: rotate(-10deg);
  }
}
@keyframes download {
  0% {
    top: 30%;
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
  100% {
    top: 55%;
    opacity: 0;
  }
}
@keyframes out {
  20% {
    top: 8%;
  }
  75%, 100% {
    top: 100%;
    opacity: 0;
    height: 0px;
  }
}
body {
  background-color: #2a2a2e;
  margin: 0;
  min-height: 100vh;
  display: grid;
  place-items: center;
}
  </style>
</head>





<body>
<div id="recorder"><img id="record" src="https://bassets.github.io/record.svg"/><img id="arrow" src="https://bassets.github.io/arrow.svg"/></div>
</body>





<script>
  
  const recordAudio = () =>
  new Promise(async (resolve) => {
    const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
    const mediaRecorder = new MediaRecorder(stream);
    const audioChunks = [];
    mediaRecorder.addEventListener("dataavailable", (event) => {
      audioChunks.push(event.data);
    });

    const start = () => mediaRecorder.start();

    const stop = () =>
      new Promise((resolve) => {
        mediaRecorder.addEventListener("stop", () => {
          const audioBlob = new Blob(audioChunks);
          const audioUrl = URL.createObjectURL(audioBlob);
          const audio = new Audio(audioUrl);
          const play = () => audio.play();
          resolve({ audioBlob, audioUrl, play });
        });
        mediaRecorder.stop();
      });
    resolve({ start, stop });
  });

const sleep = (time) => new Promise((resolve) => setTimeout(resolve, time));

var record = true;

const startRecording = async () => {
  const recording = await recordAudio();
  const recorder = document.getElementById("recorder");
  recorder.disabled = true;
  recording.start();
  while (record == true) {
    await sleep(1);
  }
  const audio = await recording.stop();
  await sleep(2000);
  audio.play();
  recorder.disabled = false;
};

document.getElementById("recorder").addEventListener("click", (e) => {
  if (document.getElementById("recorder").classList.contains("recording")) {
    document.getElementById("recorder").classList.remove("recording");
    document.getElementById("recorder").classList.add("download");
    record = false;
    setTimeout(function () {
      document.getElementById("recorder").classList.remove("download");
      document.getElementById("recorder").classList.add("out");
    }, 1000);
  } else if (
    !document.getElementById("recorder").classList.contains("recording") &&
    !document.getElementById("recorder").classList.contains("download")
  ) {
    document.getElementById("recorder").classList.remove("out");
    document.getElementById("recorder").classList.add("recording");
    record = true;
    startRecording();
  }
});

</script>
</html>
