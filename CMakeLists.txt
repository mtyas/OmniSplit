cmake_minimum_required(VERSION 3.22)

project(OmniSplit VERSION 1.0.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_compile_definitions(JUCE_WEB_BROWSER=0 JUCE_USE_CURL=0)

# Add JUCE and CLAP extensions
add_subdirectory(libs/JUCE EXCLUDE_FROM_ALL)
add_subdirectory(libs/clap-juce-extensions EXCLUDE_FROM_ALL)

set(PLUGIN_FORMATS VST3 Standalone)
if(APPLE)
    list(APPEND PLUGIN_FORMATS AU)
endif()

# ==============================================================================
# OmniSplit Audio Effect (VST3, Standalone, CLAP)
# ==============================================================================
juce_add_plugin(OmniSplit
    COMPANY_NAME "mtyas"
    BUNDLE_ID com.mtyas.omnisplit
    IS_SYNTH FALSE
    NEEDS_MIDI_INPUT FALSE
    NEEDS_MIDI_OUTPUT FALSE
    IS_MIDI_EFFECT FALSE
    EDITOR_WANTS_KEYBOARD_FOCUS FALSE
    PLUGIN_MANUFACTURER_CODE Mtya
    PLUGIN_CODE OmSp
    FORMATS ${PLUGIN_FORMATS}
    PRODUCT_NAME "OmniSplit"
)

target_sources(OmniSplit PRIVATE
    Source/DSP/TransientExtractor.h
    Source/DSP/FilterBank3.h
    Source/DSP/PreProcessors/MidSideSplitter.h
    Source/DSP/PreProcessors/DynamicSplitter.h
    Source/DSP/PreProcessors/SpectralHarmonicSplitter.h
    Source/DSP/PreProcessors/PhaseSpatialSplitter.h
    Source/DSP/Effects/BaseEffect.h
    Source/DSP/Effects/CompressorVCAEffect.h
    Source/DSP/Effects/CompressorOptoEffect.h
    Source/DSP/Effects/CompressorFETEffect.h
    Source/DSP/Effects/GateEffect.h
    Source/DSP/Effects/EqualizerParametricEffect.h
    Source/DSP/Effects/EqualizerGraphicEffect.h
    Source/DSP/Effects/EqualizerTiltEffect.h
    Source/DSP/Effects/FilterDualHPLPEffect.h
    Source/DSP/Effects/FilterBandpassEffect.h
    Source/DSP/Effects/DistortionHardClipEffect.h
    Source/DSP/Effects/DistortionTubeEffect.h
    Source/DSP/Effects/DistortionWavefolderEffect.h
    Source/DSP/Effects/FuzzGermaniumEffect.h
    Source/DSP/Effects/FuzzOctaveEffect.h
    Source/DSP/Effects/OverdriveEffect.h
    Source/DSP/Effects/TapeEffect.h
    Source/DSP/Effects/BitcrushEffect.h
    Source/DSP/Effects/GlitchStutterEffect.h
    Source/DSP/Effects/GlitchTapeStopEffect.h
    Source/DSP/Effects/GlitchReverseEffect.h
    Source/DSP/Effects/GlitchGranularEffect.h
    Source/DSP/Effects/PitchShifterEffect.h
    Source/DSP/Effects/FrequencyShifterEffect.h
    Source/DSP/Effects/ModulationChorusEffect.h
    Source/DSP/Effects/ModulationFlangerEffect.h
    Source/DSP/Effects/ModulationPhaserEffect.h
    Source/DSP/Effects/ModulationTremoloEffect.h
    Source/DSP/Effects/DelayStereoEffect.h
    Source/DSP/Effects/DelayPingPongEffect.h
    Source/DSP/Effects/DelayTapeEchoEffect.h
    Source/DSP/Effects/ReverbRoomEffect.h
    Source/DSP/Effects/ReverbHallEffect.h
    Source/DSP/Effects/ReverbPlateEffect.h
    Source/DSP/Effects/ReverbSpringEffect.h
    Source/DSP/Effects/ReverbShimmerEffect.h
    Source/DSP/EffectChain.h
    Source/DSP/TransientSplitEngine.h
    Source/DSP/Modulation/ModLFO.h
    Source/DSP/Modulation/ModEnvelopeFollower.h
    Source/DSP/Modulation/ModTuringMachine.h
    Source/DSP/Modulation/ModMatrix.h
    Source/DSP/Modulation/ModEngine.h
    Source/Parameters/ParameterIDs.h
    Source/Parameters/ParameterIDs.cpp
    Source/Presets/FactoryPresets.h
    Source/Presets/PresetManager.h
    Source/Presets/PresetManager.cpp
    Source/UI/TransientLookAndFeel.h
    Source/UI/TransientLookAndFeel.cpp
    Source/UI/RealtimeVisualizer.h
    Source/UI/RealtimeVisualizer.cpp
    Source/UI/FilterBankPanelComponent.h
    Source/UI/FilterBankPanelComponent.cpp
    Source/UI/TransientSplitPanelComponent.h
    Source/UI/TransientSplitPanelComponent.cpp
    Source/UI/AdvancedPreProcessorsComponent.h
    Source/UI/AudioRoutingDiagramComponent.h
    Source/UI/AudioRoutingDiagramComponent.cpp
    Source/UI/PreProcessorsCombinedComponent.h
    Source/UI/EffectSlotComponent.h
    Source/UI/EffectSlotComponent.cpp
    Source/UI/ChainRackComponent.h
    Source/UI/ChainRackComponent.cpp
    Source/UI/SplitControlsComponent.h
    Source/UI/SplitControlsComponent.cpp
    Source/UI/ModulatorsPanelComponent.h
    Source/UI/ModulatorsPanelComponent.cpp
    Source/UI/ModMatrixComponent.h
    Source/UI/ModMatrixComponent.cpp
    Source/UI/XYPadComponent.h
    Source/UI/XYPadComponent.cpp
    Source/UI/TransientSplitEditor.h
    Source/UI/TransientSplitEditor.cpp
    Source/TransientSplitProcessor.h
    Source/TransientSplitProcessor.cpp
)

target_include_directories(OmniSplit PRIVATE
    Source
)

target_compile_definitions(OmniSplit PUBLIC
    JUCE_WEB_BROWSER=0
    JUCE_USE_CURL=0
    JUCE_VST3_CAN_REPLACE_VST2=0
)

target_link_libraries(OmniSplit PRIVATE
    juce::juce_audio_utils
    juce::juce_audio_processors
    juce::juce_gui_basics
    juce::juce_gui_extra
    juce::juce_data_structures
    juce::juce_graphics
    juce::juce_core
    juce::juce_audio_basics
    juce::juce_audio_formats
    juce::juce_dsp
    clap_juce_extensions
)

clap_juce_extensions_plugin(TARGET OmniSplit
    CLAP_ID "com.mtyas.omnisplit"
    CLAP_FEATURES audio-effect distortion filter
)

# OmniSplit Unit Tests
add_executable(OmniSplitTests Tests/OmniSplitTests.cpp
    Source/Parameters/ParameterIDs.cpp
    Source/Presets/PresetManager.cpp
)
target_link_libraries(OmniSplitTests PRIVATE
    juce::juce_audio_basics
    juce::juce_audio_processors
    juce::juce_gui_basics
    juce::juce_core
    juce::juce_dsp
)
target_include_directories(OmniSplitTests PRIVATE Source)
