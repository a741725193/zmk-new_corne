



// features
// https://github.com/rafaelromao/keyboards



// look at an macros and config
// https://github.com/arilebedey/urob-zmk-config/blob/main/config/my.keymap


ZMK_BEHAVIOR(claude, macro,
    wait-ms = <10>;
    tap-ms = <5>;
    bindings = <&kp LS(ENTER) &kp GRAVE &kp GRAVE &kp GRAVE &kp LS(ENTER) &kp LC(V) &kp DOWN &kp LS(ENTER) &kp BSPC>;
)



//https://github.com/urob/zmk-helpers/blob/main/examples/zmk-config/config/cradio.keymap

// tap: backspace | shift + tap: delete | hold: num layer
ZMK_MOD_MORPH(bs_del_num,
    bindings = <&lt NUM BSPC>, <&kp DEL>;
    mods = <(MOD_LSFT|MOD_RSFT)>;
)





// UROB
// https://github.com/arilebedey/urob-zmk-config/blob/main/config/base.keymap

// Implement nav-word & mouse-word similar to num word
https://github.com/urob/zmk-config/blob/main/config/base.keymap#L133

https://github.com/urob/zmk-auto-layer?tab=readme-ov-file#example-general-case


// tap: space | shift + tap: dot -> space -> sticky shift | hold: activate layer
ZMK_BEHAVIOR(lt_spc, hold_tap,
    flavor = "balanced";
    tapping-term-ms = <200>;
    quick-tap-ms = <QUICK_TAP_MS>;
    bindings = <&mo>, <&spc_morph>;
)
SIMPLE_MORPH(spc_morph, SFT, &kp SPACE, &dot_spc)
ZMK_BEHAVIOR(dot_spc, macro,
    wait-ms = <0>;
    tap-ms = <5>;
    bindings = <&kp DOT &kp SPACE &sk LSHFT>;
)

// tap: backspace | lshft + tap: delete | rshft + tap: shift-delete
ZMK_BEHAVIOR(bs_del, mod_morph,
    bindings = <&kp BSPC>, <&kp DEL>;
    mods = <(MOD_LSFT|MOD_RSFT)>;
    keep-mods = <MOD_RSFT>;
)

// long-tap action on hold, short-tap action on all interrupts
#define MT_CORE \
    flavor = "tap-preferred"; \
    tapping-term-ms = <220>; \
    quick-tap-ms = <220>; \
    hold-trigger-key-positions = <0>;


// NAV_XXX 
&mt { MT_CORE };
ZMK_BEHAVIOR(mt_home, hold_tap, bindings = <&masked_home>, <&kp>; MT_CORE)
ZMK_BEHAVIOR(mt_end,  hold_tap,  bindings = <&masked_end>, <&kp>; MT_CORE)

#define NAV_LEFT  &mt_home 0   LEFT    // tap: left  | long-tap: beginning of line
#define NAV_RIGHT &mt_end 0    RIGHT   // tap: right | long-tap: end       of line
#define NAV_UP    &mt LC(HOME) UP      // tap: up    | long-tap: beginning of document
#define NAV_DOWN  &mt LC(END)  DOWN    // tap: down  | long-tap: end       of document
#define NAV_BSPC  &mt LC(BSPC) BSPC    // tap: bspc  | long-tap: delete word backward
#define NAV_DEL   &mt LC(DEL)  DEL     // tap: del   | long-tap: delete word forward

// mask CTRL when holding left/right to avoid accidental jumps to beginning/end of document
#define MASK_MODS(NAME, MODS, BINDING) \
    ZMK_BEHAVIOR(NAME, mod_morph, \
        bindings = <BINDING>, <BINDING>; \
        mods = <MODS>; \
    )
MASK_MODS(masked_home, (MOD_LCTL), &kp HOME)
MASK_MODS(masked_end,  (MOD_LCTL), &kp END)

// // look at config, keypos.h,  combos behaviour etc 
// // https://github.com/englmaxi/zmk-config/blob/main/config/keymap/keymap.dtsi
behaviours -

/ {
    tri-state {
        alt_tabber: swapper {
            compatible = "zmk,behavior-tri-state";
            #binding-cells = <0>;
            bindings = <&kt LALT>, <&kp TAB>, <&kt LALT>;
            ignored-key-positions = <RT2 RM1 RM2 RM3>; // arrow keys
            timeout-ms = <5000>;
            display-name = "Swapper";
        };

        sel_word: sel_word {
            compatible = "zmk,behavior-tri-state";
            #binding-cells = <0>;
            bindings = <&sel_word_base>, <&kp LS(LC(RIGHT))>, <&none>;
            tap-ms = <0>;
            display-name = "Select Word";
        };

        sel_line: sel_line {
            compatible = "zmk,behavior-tri-state";
            #binding-cells = <0>;
            bindings = <&sel_line_base>, <&sel_line_next>, <&none>;
            tap-ms = <0>;
            display-name = "Select Line";
        };
    };


    antecedent-morph {
        ad_u: adaptive_u {
            compatible = "zmk,behavior-antecedent-morph";
            #binding-cells = <0>;
            defaults = <&kp U>;
            bindings =   <&kp DE_EQUAL>, <&kp DE_EQUAL>;
            antecedents = <DE_LT           DE_GT>;
            max-delay-ms = <1000>;
            display-name = "Adaptive U";
        };

        ad_comma: adaptive_comma {
            compatible = "zmk,behavior-antecedent-morph";
            #binding-cells = <0>;
            defaults = <&kp DE_COMMA>;
            bindings =   <&kp DE_EQUAL>, <&include_macro>, <&kp DE_FSLH>;
            antecedents = <DE_EXCL         DE_HASH           DE_DOT>;
            max-delay-ms = <1000>;
            display-name = "Adaptive Comma";
        };

        ad_e: adaptive_e {
            compatible = "zmk,behavior-antecedent-morph";
            #binding-cells = <0>;
            defaults = <&kp E>;
            bindings =   <&kp DE_GT>, <&kp DE_GT>, <&kp DE_GT>;
            antecedents = <DE_MINUS     DE_EQUAL     DE_PIPE>;
            max-delay-ms = <1000>;
            display-name = "Adaptive E";
        };
    };




---

KEYMAP


/ {
    hold-tap {
        lt_del: layer_tap_del {
            compatible = "zmk,behavior-hold-tap";
            #binding-cells = <2>;
            flavor = "balanced";
            tapping-term-ms = <220>;
            quick-tap-ms = <220>;
            bindings = <&mo>, <&bspc_del>;
            display-name = "Layer-BSp";
        };

        lt_qt: layer_tap_qt {
            compatible = "zmk,behavior-hold-tap";
            #binding-cells = <2>;
            flavor = "balanced";
            tapping-term-ms = <220>;
            quick-tap-ms = <220>;
            bindings = <&mo>, <&qt_grv>;
            display-name = "Layer-Qt";
        };

        lt_e: layer_tap_ad_e {
            compatible = "zmk,behavior-hold-tap";
            #binding-cells = <2>;
            flavor = "balanced";
            tapping-term-ms = <220>;
            quick-tap-ms = <220>;
            bindings = <&mo>, <&ad_e>;
            display-name = "Layer-AdE";
        };




/ {
    mod-morph {
        SM(qexcl,    SM Qmark-Excl, &kp DE_QMARK,  &kp DE_EXCL)
        SM(bspc_del, SM BSp-Del,    &kp BSPC,      &kp DEL)

        SM(sqt_dqt,       SM Sqt-Dqt,       &kp DE_SQT,   &kp DE_DQT)
        CM(qt_grv,        CM Qt-Grv,        &sqt_dqt,     &kp DE_ACUTE)
        SM(sqt_dqt_macro, SM Macro Sqt-Dqt, &sqt_macro,   &dqt_macro)
        SM(grv_dgrv,      SM Grv-DGrqu,     &kp DE_GRAVE, &dgrv_macro)

        SM(lpar_lbkt, SM LPar-LBkt,     &kp DE_LPAR, &kp DE_LBKT)
        SM(rpar_rbkt, SM RPar-RBkt,     &kp DE_RPAR, &kp DE_RBKT)
        SM(par_bkt,   SM Macro Par-Bkt, &par_macro,  &bkt_macro)

        SM(lbrc_lt, SM LBrc-LT,       &kp DE_LBRC, &kp DE_LT)
        SM(rbrc_gt, SM RBrc-GT,       &kp DE_RBRC, &kp DE_GT)
        SM(brc_lgt, SM Macro Brc-LGT, &brc_macro,  &lgt_macro)

        SM(p_tab_desk, SM Prev Tab-Desk, &kp LC(LS(TAB)), &kp LC(LG(LEFT)))
        SM(n_tab_desk, SM Next Tab-Desk, &kp LC(TAB), &kp LC(LG(RIGHT)))

        MASKED(left_masked, Masked Left, (MOD_RSFT|MOD_LSFT), &kp LEFT)
        MASKED(u_masked,    Masked U,    (MOD_RSFT|MOD_LSFT), &kp U)
    };




// homerow individual finger timings 
// https://github.com/englmaxi/zmk-config/blob/main/config/keymap/behaviors.dtsi



/ {
    home-row-mod {
        HM(hsl,   HRM Shift Left,      &kp,  KEYS_R,   0, hold-while-undecided;)
        HM(hcl,   HRM Ctrl Left,       &kp,  KEYS_R, 200, hold-while-undecided;)
        HM(hml,   HRM Mods Left,       &kp,  KEYS_R, 200)
        HM(hml_m, HRM Mods-Mouse Left, &mkp, KEYS_R, 200)
        HM(hsr,   HRM Shift Right,     &kp,  KEYS_L,   0, hold-while-undecided;)
        HM(hcr,   HRM Ctrl Right,      &kp,  KEYS_L, 200, hold-while-undecided;)
        HM(hmr,   HRM Mods Left,       &kp,  KEYS_L, 200)
    };
};
