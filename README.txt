Hi!

I have an idea for a signaling simulating halt decider that forks the
simulation into two branches if the input calls the halt decider as
per [Strachey 1965]'s "Impossible Program":

void P(void (*x)())
{
	if (H(x, x))
		infinite_loop: goto infinite_loop;
	return;
}

int main()
{
	std::cout << "Input halts: " << H(P, P) << std::endl;
}

When the simulator detects the call to H in P it forks the simulation
into a non-halting branch (returning 0 to P) and a halting branch
(returning 1 to P) and continues the simulation of these two branches
in parallel.

If the non-halting branch is determined to halt AND the halting branch
is determined to not halt then pathology is detected and reported via
a sNaP (signaling Not a Program) signal (analogous to IEEE 754's 
sNaN (signaling Not a Number) signal)

If EITHER branch is determined to be correctly decided then that will
be the decision of the halting decider.

Crucially this scheme will handle (and correctly decide) the
following case whereby the result of H is discarded by the input:

void Px(void (*x)())
{
	(void) H(x, x);
	return;
}

Obviously my idea necessitates extending the definition of a halt
decider:

1) Decider decision is HALTS if input halts.
2) Decider decision is NON-HALTING if input does not halt.
3) Decider rejects pathological input as invalid by signaling sNaP.

Thoughts?  I am probably missing something obvious as my idea
appears to refute [Strachey 1965] and associated HP proofs which
great minds have mulled over for decades.
