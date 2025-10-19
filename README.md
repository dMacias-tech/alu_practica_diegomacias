# alu_practica_diegomacias
alu_practica_diegomacias
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: ITESO
// Engineer: Diego Macias
//////////////////////////////////////////////////////////////////////////////////

module alu_top(
    input  wire[3:0] A,      // operandos
    input  wire[3:0] B,
    input  wire[1:0] opcode, // 00: suma, 01: resta, 10: OR, 11: AND
    output wire[6:0] seg,    // segmentos del display
    output wire[3:0] T,      // control de displays
    output wire[3:0] LED     // C, O, N, Z
    );

    wire [3:0] arith_R, logic_R;
    wire [3:0] result;
    wire C, O, N, Z;

    // Instancia de unidad aritmética
    Oper_arit UA (
        .A(A),
        .B(B),
        .sel_sub(opcode[0]),
        .R(arith_R),
        .C(C),
        .O(O),
        .N(N),
        .Z(Z)
    );

    // Instancia de unidad lógica
    Oper_log UL (
        .A(A),
        .B(B),
        .sel_or(opcode[0]),
        .R(logic_R)
    );

    // Multiplexor de resultado
    assign result = (opcode == 2'b00) ? arith_R :
                    (opcode == 2'b01) ? arith_R :
                    (opcode == 2'b10) ? logic_R :
                    (opcode == 2'b11) ? logic_R :arith_R;
                    
                    
    // Banderas
    assign LED[0] = C;
    assign LED[1] = O;
    assign LED[2] = N;
    assign LED[3] = Z;
    
    //display transitores
    assign T = 4'b1110;

    // Mostrar resultado en display
    decoder_bin_hex_7seg display (
        .w(result[3]),
        .x(result[2]),
        .y(result[1]),
        .z(result[0]),
        .seg_a(seg[0]),
        .seg_b(seg[1]),
        .seg_c(seg[2]),
        .seg_d(seg[3]),
        .seg_e(seg[4]),
        .seg_f(seg[5]),
        .seg_g(seg[6])
    );
endmodule
