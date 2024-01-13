
`jpegls_pc_parameters = charls_jpegls_pc_parameters`

```c++
struct coding_parameters final
{
    int32_t near_lossless;
    uint32_t restart_interval;
    charls::interleave_mode interleave_mode;
    color_transformation transformation;
};

struct charls_jpegls_pc_parameters CHARLS_FINAL
{
    int32_t maximum_sample_value;
    int32_t threshold1;
    int32_t threshold2;
    int32_t threshold3;
    /// Value at which the counters A, B, and N are halved.
    int32_t reset_value;
};

struct charls_frame_info CHARLS_FINAL
{
    uint32_t width;
    uint32_t height;
    int32_t bits_per_sample;
    int32_t component_count;
};
```

```c++
enum class interleave_mode
{
    /// The data is encoded and stored as component for component: RRRGGGBBB.
    none = impl::CHARLS_INTERLEAVE_MODE_NONE,
    /// The interleave mode is by line. A full line of each component is encoded before moving to the next line.
    line = impl::CHARLS_INTERLEAVE_MODE_LINE,
    /// The data is encoded and stored by sample. For RGB color images this is the format like RGBRGBRGB.
    sample = impl::CHARLS_INTERLEAVE_MODE_SAMPLE,
};
```
scan_codec.h

```cpp
template<typename Traits>
bool precomputed_quantization_lut_available(const Traits& traits, const int32_t threshold1, const int32_t threshold2, const int32_t threshold3) noexcept
```

