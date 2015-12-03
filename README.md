#include "spimcore.h"
/* Group 25
Matthew Powers ma 548393
Mark Behler    " "*/

/* ALU */
/* 10 Points */
void ALU(unsigned A, unsigned B, char ALUControl, unsigned *ALUresult, char *Zero)
{
	switch ((int)ALUControl)
	{
		//ALUresult = A + B
		case 000:
			*ALUresult = A + B;
			break;

		//ALUresult = A - B
		case 001:
			*ALUresult = A - B;
			break;

		//ALUresult = 1 iff A < B, otherwise ALU result = 0. A and B are signed integers
		case 010:
			if ((signed)A < (signed)B)
			{
				*ALUresult = 1;
			}
			else
			{
				*ALUresult = 0;
			}
			break;

		//same as the above case except A and B are unsigned integers
		case 011:
			if (A < B)
			{
				*ALUresult = 1;
			}
			else
			{
				*ALUresult = 0;
			}
			break;

		//ALUresult = bitwise A AND B
		case 100:
			*ALUresult = A & B;
			break;

		//ALU result = bitwise A OR B
		case 101:
			*ALUresult = A | B;
			break;

		//Shift B left 16 bits
		case 110:
			B << 16;
			break;

		//ALUresult = not A
		case 111:
			*ALUresult = ~A;
			break;
	}

	//set Zero = 1 if ALUresult = 0
	if (*ALUresult == 0)
		*Zero = 1;
	else
		*Zero = 0;

}

/* instruction fetch */
/* 10 Points */
int instruction_fetch(unsigned PC, unsigned *Mem, unsigned *instruction)
{
	//get actual location to check
	unsigned memIndex = PC >> 2;

	//check for word alignment 
	if (PC % 4 == 0)
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
	//AND with according 32 bits and shift accordingly 
	*op = (instruction & 0xFC000000 /*0b11111100000000000000000000000000*/) >> 26; //instr[31-26]
	*r1 = (instruction & 0x03E00000 /*0b00000011111000000000000000000000*/) >> 21; //instr[25-21]
	*r2 = (instruction & 0x001F0000 /*0b00000000000111110000000000000000*/) >> 16; //instr[20-16]
	*r3 = (instruction & 0x0000F800 /*0b00000000000000001111100000000000*/) >> 11; //instr[15-11]
	*funct = instruction & 0x0000003F /*0b00000000000000000000000000111111*/; //instr[5-0]
	*offset = instruction & 0x0000FFFF /*0b00000000000000001111111111111111*/; //instr[15-0]
	*jsec = instruction & 0x03FFFFFF /*0b00000011111111111111111111111111*/; //instr[25-0]
}



/* instruction decode */
/* 15 Points */
int instruction_decode(unsigned op, struct_controls *controls)
{
		switch (op)
		{
		case 0:		//	000000 0	R format
			controls->RegDst = 0;
			controls->Jump = 0;
			controls->Branch = 0;
			controls->MemRead = 0;
			controls->MemtoReg = 0;
			controls->ALUOp = 7;
			controls->MemWrite = 0;
			controls->ALUSrc = 0;
			controls->RegWrite = 1;
			break;
		case 2:		//	000010 2	jump
			controls->RegDst = 0;
			controls->Jump = 1;
			controls->Branch = 0;
			controls->MemRead = 0;
			controls->MemtoReg = 0;
			controls->ALUOp = 0;
			controls->MemWrite = 0;
			controls->ALUSrc = 0;
			controls->RegWrite = 0;
			break;
		case 4:		//	000100 4	beq			branch eq
			controls->RegDst = 2;
			controls->Jump = 0;
			controls->Branch = 1;
			controls->MemRead = 0;
			controls->MemtoReg = 2;
			controls->ALUOp = 1;
			controls->MemWrite = 0;
			controls->ALUSrc = 0;
			controls->RegWrite = 0;
			break;
		case 8:		//	001000 8	addi		add immediate
			controls->RegDst = 0;
			controls->Jump = 0;
			controls->Branch = 0;
			controls->MemRead = 0;
			controls->MemtoReg = 0;
			controls->ALUOp = 0;
			controls->MemWrite = 0;
			controls->ALUSrc = 1;
			controls->RegWrite = 1;
			break;
		case 10:	//	001010 10	slti		set less than immediate
			controls->RegDst = 1;
			controls->Jump = 0;
			controls->Branch = 0;
			controls->MemRead = 0;
			controls->MemtoReg = 0;
			controls->ALUOp = 2;
			controls->MemWrite = 0;
			controls->ALUSrc = 0;
			controls->RegWrite = 1;
			break;
		case 11:	//	001011 11	sltiu		set less than immediate u
			controls->RegDst = 1;
			controls->Jump = 0;
			controls->Branch = 0;
			controls->MemRead = 0;
			controls->MemtoReg = 0;
			controls->ALUOp = 3;
			controls->MemWrite = 0;
			controls->ALUSrc = 0;
			controls->RegWrite = 1;
			break;
		case 15:	//	001111 15	lui			load upper immediate
			controls->RegDst = 0;
			controls->Jump = 0;
			controls->Branch = 0;
			controls->MemRead = 0;
			controls->MemtoReg = 0;
			controls->ALUOp = 6;
			controls->MemWrite = 0;
			controls->ALUSrc = 1;
			controls->RegWrite = 1;
			break;
		case 35:	//	100011 35	lw			load word
			controls->RegDst = 0;
			controls->Jump = 0;
			controls->Branch = 0;
			controls->MemRead = 1;
			controls->MemtoReg = 1;
			controls->ALUOp = 0;
			controls->MemWrite = 0;
			controls->ALUSrc = 1;
			controls->RegWrite = 1;
			break;
		case 43:	//	101011 43	sw			store word
			controls->RegDst = 2;
			controls->Jump = 0;
			controls->Branch = 0;
			controls->MemRead = 0;
			controls->MemtoReg = 2;
			controls->ALUOp = 0;
			controls->MemWrite = 1;
			controls->ALUSrc = 1;
			controls->RegWrite = 0;
			break;
		default:
			return 1;
		}
	return 0;
}

/* Read Register */
/* 5 Points */
void read_register(unsigned r1, unsigned r2, unsigned *Reg, unsigned *data1, unsigned *data2)
{
	// Go into respective registers given and copy the data from them
	*data1 = Reg[r1];
	*data2 = Reg[r2];
}


/* Sign Extend */
/* 10 Points */
void sign_extend(unsigned offset, unsigned *extended_value)
{
	// see if the offset is negative then extend
	if (offset >> 15 == 1)
		*extended_value = offset | 0xFFFF0000;
	else // Upper bits should already be zero
		*extended_value = offset;
}

/* ALU operations */
/* 10 Points */
int ALU_operations(unsigned data1, unsigned data2, unsigned extended_value, unsigned funct, char ALUOp, char ALUSrc, unsigned *ALUresult, char *Zero)
{
	//check ALUSrc to see what data we are operating on
	if (ALUSrc == 1)
	{
		data2 = extended_value;
	}
	//7 in ALUOp means that it's an R type
	if (ALUOp == 7)
	{
		switch (funct)
		{
			//add
		case 32:
			ALUOp = 0;
			break;
			//sub
		case 34:
			ALUOp = 1;
			break;
			//slt
		case 42:
			ALUOp = 2;
			//sltu
		case 43:
			ALUOp = 3;
			//and
		case 36:
			ALUOp = 4;
			//or 
		case 37:
			ALUOp = 5;
			//lui
		case 6:
			ALUOp = 6;
			//nor
		case 39:
			ALUOp = 7;
		default:
			return 1;
		}
		ALU(data1, data2, ALUOp, ALUresult, Zero);
	}
	//send to ALU if not func
	else
	{
		ALU(data1, data2, ALUOp, ALUresult, Zero);
	}
}

/* Read / Write Memory */
/* 10 Points */
int rw_memory(unsigned ALUresult, unsigned data2, char MemWrite, char MemRead, unsigned *memdata, unsigned *Mem)
{
	// Reading from memory
	if (MemRead == 1) {
		// check to see if the address is good
		if ((ALUresult % 4) == 0)
			*memdata = Mem[ALUresult >> 2];
		else // return since its an invaild memory address
			return 1;
	}
	// Writing to Memory
	if (MemWrite == 1) {
		// Check to see if the address is good
		if ((ALUresult % 4) == 0)
			Mem[ALUresult >> 2] = data2;
		else // return since its an invaild memory address
			return 1;
	}
	return 0;
}


/* Write Register */
/* 10 Points */
void write_register(unsigned r2, unsigned r3, unsigned memdata, unsigned ALUresult, char RegWrite, char RegDst, char MemtoReg, unsigned *Reg)
{
	// Actually check if your writing to the register lolz
	if (RegWrite == 1) {
		// Check if writing with memdata but with the first register
		if (MemtoReg == 1 && RegDst == 0)
			Reg[r2] = memdata;
		// Check if writing with memdata but with the second register
		else if (MemtoReg == 1 && RegDst == 1)
			Reg[r3] = memdata;
		// Check that we are writing to the first register with ALUresult
		else if (MemtoReg == 0 && RegDst == 0)
			Reg[r2] = ALUresult;
		// Check that we are writing to the second register with ALUresult
		else if (MemtoReg == 0 && RegDst == 1)
			Reg[r3] = ALUresult;
	}
	// Otherwise do nothing
}

/* PC update */
/* 10 Points */
void PC_update(unsigned jsec, unsigned extended_value, char Branch, char Jump, char Zero, unsigned *PC)
{
	*PC += 4;
	//check to see if we are jumping
	if (Jump == 1)
		*PC = (jsec << 2) | (*PC & 0xf0000000);
	//if we are branching and got a zero add the offset
	if (Branch == 1 && Zero == 1)
		*PC += extended_value << 2;
}
