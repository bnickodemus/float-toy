float CKDEDlg::canardConvertFloat16ToNativeFloat(uint16_t value)
{
    //CANARD_ASSERT(sizeof(float) == CANARD_SIZEOF_FLOAT); // should be 4...

    union FP32
    {
        uint32_t u;
        float f;
    };

    const union FP32 magic = { (254UL - 15UL) << 23U };
    const union FP32 was_inf_nan = { (127UL + 16UL) << 23U };
    union FP32 out;

    out.u = (value & 0x7FFFU) << 13U;
    out.f *= magic.f;
    if (out.f >= was_inf_nan.f)
    {
        out.u |= 255UL << 23U;
    }
    out.u |= (value & 0x8000UL) << 16U;

    return out.f;
}

uint16_t CKDEDlg::canardConvertNativeFloatToFloat16(float value)
{
    union FP32
    {
        uint32_t u;
        float f;
    };

    const union FP32 f32inf = { 255UL << 23U };
    const union FP32 f16inf = { 31UL << 23U };
    const union FP32 magic = { 15UL << 23U };
    const uint32_t sign_mask = 0x80000000UL;
    const uint32_t round_mask = 0xFFFFF000UL;

    union FP32 in;
    in.f = value;
    uint32_t sign = in.u & sign_mask;
    in.u ^= sign;

    uint16_t out = 0;

    if (in.u >= f32inf.u)
    {
        out = (in.u > f32inf.u) ? (uint16_t)0x7FFFU : (uint16_t)0x7C00U;
    }
    else
    {
        in.u &= round_mask;
        in.f *= magic.f;
        in.u -= round_mask;
        if (in.u > f16inf.u)
        {
            in.u = f16inf.u;
        }
        out = (uint16_t)(in.u >> 13U);
    }

    out |= (uint16_t)(sign >> 16U);

    return out;
}

