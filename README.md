import argparse
import io
!pip install google.cloud 
!pip install SpeechRecognition
from google.protobuf import wrappers_pb2


def transcribe_file_with_enhanced_model():
    #Transcribing the Audio using an Enhanced Model.

    client = SpeechRecognition.SpeechClient()
    speech_file = "resources/commercial_mono.wav"

    with io.open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    audio = SpeechRecognition.RecognitionAudio (content=content)
    config = SpeechRecognition.RecognitionConfig (
        encoding=SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=8000,
        language_code="en-US",
        use_enhanced=True,
        model="Conversations between two people",)

    response = client.recognize(config=config, audio=audio)
    for i, result in enumerate(response.results):
        alternative = result.alternatives[0]
        print("-" * 20)
        print(u"First alternative of result {}".format(i))
        print(u"Transcript: {}".format(alternative.transcript))


def transcribe_file_with_metadata():
    #Sending Request which includes Recognition of the Metadata.

    client = SpeechRecognition.SpeechClient()
    speech_file = "resources/commercial_mono.wav"

    with io.open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    # Constructing  a Recognition Metadata Object.
    # Most metadata fields are specified as enumerators which can be found in speech.enums.RecognitionMetadata

    metadata = SpeechRecognition.RecognitionMetadata ()
    metadata.interaction_type = SpeechRecognition.RecognitionMetadata.InteractionType.DISCUSSION
    metadata.microphone_distance = (SpeechRecognition.RecognitionMetadata.MicrophoneDistance.NEARFIELD)
    metadata.recording_device_type = (SpeechRecognition.RecognitionMetadata.RecordingDeviceType.SMARTPHONE)

    # Some Metadata fields are Strings
    metadata.recording_device_name = "Smartphone"
    # Some  Metadata fields are Integers, for instance the 6 digit NAICS code
    metadata.industry_naics_code_of_audio = 519190

    audio = SpeechRecognition.RecognitionAudio(content=content)
    config = SpeechRecognition.RecognitionConfig (
        encoding=SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=8000,
        language_code="en-US",
        # Add this in the request to send metadata.
        metadata=metadata,)

    response = client.recognize(config=config, audio=audio)
    for i, result in enumerate(response.results):
        alternative = result.alternatives[0]
        print("-" * 20)
        print(u"First alternative of result {}".format(i))
        print(u"Transcript: {}".format(alternative.transcript))


def transcribe_file_with_auto_punctuation():
    #Transcribing Audio with Auto Punctuation Enabled.
    
    client = SpeechRecognition.SpeechClient()
    speech_file = "resources/commercial_mono.wav"

    with io.open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    audio = SpeechRecognition.RecognitionAudio(content=content)
    config = SpeechRecognition.RecognitionConfig (
        encoding=SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=8000,
        language_code="en-US",
        # Enabling Automatic Punctuation
        enable_automatic_punctuation=True,)

    response = client.recognize(config=config, audio=audio)

    for i, result in enumerate(response.results):
        alternative = result.alternatives[0]
        print("-" * 20)
        print(u"First alternative of result {}".format(i))
        print(u"Transcript: {}".format(alternative.transcript))


def transcribe_file_with_diarization():
    #Transcribing Audio Synchronously with Diarization.
   
    client = SpeechRecognition.SpeechClient()
    speech_file = "resources/commercial_mono.wav"

    with open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    audio =SpeechRecognition.RecognitionAudio(content=content)

    diarization_config = SpeechRecognition.SpeakerDiarizationConfig(
      enable_speaker_diarization=True,
      min_speaker_count=2,
      max_speaker_count=10,)

    config = SpeechRecognition.RecognitionConfig(
        encoding=SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=8000,
        language_code="en-US",
        diarization_config=diarization_config,)

    print("Please Wait for the Operation to Complete.")
    response = client.recognize(config=config, audio=audio)

    # To get all the Words with the Speaker tags we will 
    # have to take the Words List from the Last Result:

    result = response.results[-1]
    words_info = result.alternatives[0].words

    for word_info in words_info:
        print(
            u"word: '{}', speaker_tag: {}".format(word_info.word, word_info.speaker_tag))


def transcribe_file_with_multichannel():
    #Transcribing Audio File Synchronously with Multi Channel.

    client = SpeechRecognition.SpeechClient()
    speech_file = "resources/Google_Gnome.wav"

    with open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    audio = SpeechRecognition.RecognitionAudio(content=content)
    config = SpeechRecognition.RecognitionConfig (
        encoding=SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=16000,
        language_code="en-US",
        audio_channel_count=1,
        enable_separate_recognition_per_channel=True,)

    response = client.recognize(config=config, audio=audio)
    for i, result in enumerate(response.results):
        alternative = result.alternatives[0]
        print("-" * 20)
        print("First alternative of result {}".format(i))
        print(u"Transcript: {}".format(alternative.transcript))
        print(u"Channel Tag: {}".format(result.channel_tag))


def transcribe_file_with_multilanguage():
    #Transcribing Audio Synchronously with Multi Language.
  
    client =  SpeechRecognition.SpeechClient()
    speech_file = "resources/multi.wav"
    first_language = "en-US"
    second_language = "es"

    with open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    audio =  SpeechRecognition.RecognitionAudio(content=content)

    config = SpeechRecognition.RecognitionConfig (
        encoding= SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=44100,
        audio_channel_count=2,
        language_code=first_language,
        alternative_language_codes=[second_language],)

    print("Waiting for operation to complete...")
    response = client.recognize(config=config, audio=audio)

    for i, result in enumerate(response.results):
        alternative = result.alternatives[0]
        print("-" * 20)
        print(u"First alternative of result {}: {}".format(i, alternative))
        print(u"Transcript: {}".format(alternative.transcript))


def transcribe_file_with_word_level_confidence():
    # Transcribing Audio file Synchronously with Word Level Confidence

    client = SpeechRecognition.SpeechClient()
    speech_file = "resources/Google_Gnome.wav"

    with open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    audio = SpeechRecognition.RecognitionAudio(content=content)
    config = SpeechRecognition.RecognitionConfig (
        encoding=SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=16000,
        language_code="en-US",
        enable_word_confidence=True,)

    response = client.recognize(config=config, audio=audio)

    for i, result in enumerate(response.results):
        alternative = result.alternatives[0]
        print("-" * 20)
        print("First alternative of result {}".format(i))
        print(u"Transcript: {}".format(alternative.transcript))
        print(
            u"First Word and Confidence: ({}, {})".format(
                alternative.words[0].word, alternative.words[0].confidence))


def transcribe_file_with_spoken_punctuation_end_emojis():
    #Transcribing Audio with Spoken Punctuation and Emojis Enabled
  
    client = SpeechRecognition.SpeechClient()
    speech_file = "resources/commercial_mono.wav"

    with io.open(speech_file, "rb") as audio_file:
        content = audio_file.read()

    audio = SpeechRecognition.RecognitionAudio(content=content)

    config = SpeechRecognition.RecognitionConfig (
        encoding=SpeechRecognition.RecognitionConfig.AudioEncoding.LINEAR16,
        sample_rate_hertz=8000,
        language_code="en-US",
        # Enabling Spoken Punctuation
        enable_spoken_punctuation=wrappers_pb2.BoolValue(value=True),
        # Enabling Spoken Emojis
        enable_spoken_emojis=wrappers_pb2.BoolValue(value=True),)

    response = client.recognize(config=config, audio=audio)

    for i, result in enumerate(response.results):
        alternative = result.alternatives[0]
        print("-" * 20)
        print(u"First alternative of result {}".format(i))
        print(u"Transcript: {}".format(alternative.transcript))


# Main Function to call different Functions According to the Audio Transcript:-

''' if __name__ == "__main__":
    parser = argparse.ArgumentParser (
        description=__doc__, formatter_class=argparse.RawDescriptionHelpFormatter
    )

    parser.add_argument("command")
    args = parser.parse_args()

    if args.command == "enhanced-model":
        transcribe_file_with_enhanced_model()
    elif args.command == "metadata":
        transcribe_file_with_metadata()
    elif args.command == "punctuation":
        transcribe_file_with_auto_punctuation()
    elif args.command == "diarization":
        transcribe_file_with_diarization()
    elif args.command == "multi-channel":
        transcribe_file_with_multichannel()
    elif args.command == "multi-language":
        transcribe_file_with_multilanguage()
    elif args.command == "word-level-conf":
        transcribe_file_with_word_level_confidence()
    elif args.command == "spoken-punctuation-emojis":
        transcribe_file_with_spoken_punctuation_end_emojis() '''

  





  




