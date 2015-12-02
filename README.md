// project.c
#include "spimcore.h"
/* Group 25
Matthew Powers
Mark Behler */

/* ALU */
/* 10 Points */
void ALU(unsigned A, unsigned B, char ALUControl, unsigned *ALUresult, char *Zero)
{

}

/* instruction fetch */
/* 10 Points */
int instruction_fetch(unsigned PC, unsigned *Mem, unsigned *instruction)
{
	//get actual location to check
	unsigned memIndex = PC >> 2;

	//check for word alignment 
	if (PC % 4 = 0)
	{
		*instruction = Mem[memIndex];
		return 0;
	}

	else
	{
		return 1;
	}
}


/* instruction partition */
/* 10 Points */
void instruction_partition(unsigned instruction, unsigned *op, unsigned *r1, unsigned *r2, unsigned *r3, unsigned *funct, unsigned *offset, unsigned *jsec)
{
	//shift to correct partition then mask the bits
	*op = (instruction >> 26) & 0b11111100000000000000000000000000; //instr[31-26]
	*r1 = (instruction >> 21) & 0b00000011111000000000000000000000; //instr[25-21]
	*r2 = (instruction >> 16) & 0b00000000000111110000000000000000; //instr[20-16]
	*r3 = (instruction >> 11) & 0b00000000000000001111100000000000; //instr[15-11]
	*funct = instruction & 0b00000000000000000000000000111111; //instr[5-0]
	*offset = instruction & 0b00000000000000001111111111111111; //instr[15-0]
	*jsec = instruction & 0b00000011111111111111111111111111; //instr[25-0]
}



/* instruction decode */
/* 15 Points */
int instruction_decode(unsigned op, struct_controls *controls)
{
	switch (op)
	{
		case 0:		//R-type!
		{//set these controls to the correct value and continue to do so for each case!
			controls->ALUOp;
			controls->ALUSrc;
			controls->Branch;
			controls->Jump;
			controls->MemRead;
			controls->MemtoReg;
			controls->MemWrite;
			controls->RegDst;
			controls->RegWrite;
		}
	}
}

/* Read Register */
/* 5 Points */
void read_register(unsigned r1, unsigned r2, unsigned *Reg, unsigned *data1, unsigned *data2)
{

}


/* Sign Extend */
/* 10 Points */
void sign_extend(unsigned offset, unsigned *extended_value)
{

}

/* ALU operations */
/* 10 Points */
int ALU_operations(unsigned data1, unsigned data2, unsigned extended_value, unsigned funct, char ALUOp, char ALUSrc, unsigned *ALUresult, char *Zero)
{

}

/* Read / Write Memory */
/* 10 Points */
int rw_memory(unsigned ALUresult, unsigned data2, char MemWrite, char MemRead, unsigned *memdata, unsigned *Mem)
{

}


/* Write Register */
/* 10 Points */
void write_register(unsigned r2, unsigned r3, unsigned memdata, unsigned ALUresult, char RegWrite, char RegDst, char MemtoReg, unsigned *Reg)
{

}

/* PC update */
/* 10 Points */
void PC_update(unsigned jsec, unsigned extended_value, char Branch, char Jump, char Zero, unsigned *PC)
{

}

