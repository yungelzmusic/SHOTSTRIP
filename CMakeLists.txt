cmake_minimum_required(VERSION 3.22)
project(ShotsFXChannel VERSION 0.1.0)
set(CMAKE_CXX_STANDARD 17)

include(FetchContent)
FetchContent_Declare(JUCE
    GIT_REPOSITORY https://github.com/juce-framework/JUCE.git
    GIT_TAG 8.0.4
    GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(JUCE)

# Pass -DAAX_SDK_PATH=/path/to/aax-sdk to also build the AAX (Pro Tools) version.
set(AAX_SDK_PATH "" CACHE PATH "Path to the Avid AAX SDK (optional)")
option(COPY_AFTER_BUILD "Copy plugins into system plugin folders after building" ON)

if(APPLE)
    set(FMTS VST3 AU Standalone)
else()
    set(FMTS VST3 Standalone)
endif()

if(AAX_SDK_PATH)
    juce_set_aax_sdk_path("${AAX_SDK_PATH}")
    list(APPEND FMTS AAX)
endif()

juce_add_plugin(RoyalStrip
    COMPANY_NAME "Hot Shot Da 1"
    IS_SYNTH FALSE
    NEEDS_MIDI_INPUT FALSE
    NEEDS_MIDI_OUTPUT FALSE
    PLUGIN_MANUFACTURER_CODE Hsfx
    PLUGIN_CODE Sfxc
    AAX_IDENTIFIER com.hotshotda1.shotsfxchannel
    FORMATS ${FMTS}
    PRODUCT_NAME "ShotsFX Channel"
    COPY_PLUGIN_AFTER_BUILD ${COPY_AFTER_BUILD})

target_sources(RoyalStrip PRIVATE
    PluginProcessor.cpp
    PluginEditor.cpp)

target_compile_definitions(RoyalStrip PUBLIC
    JUCE_WEB_BROWSER=0
    JUCE_USE_CURL=0
    JUCE_VST3_CAN_REPLACE_VST2=0)

target_link_libraries(RoyalStrip
    PRIVATE juce::juce_audio_utils
    PUBLIC  juce::juce_recommended_config_flags
            juce::juce_recommended_lto_flags
            juce::juce_recommended_warning_flags)
