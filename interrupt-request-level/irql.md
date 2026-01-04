# Windows Interrupt Request Level (IRQL)

Windows has the notion of Interrupt Request Levels (IRQLs). This refers to the prioritization of different interrupt and scheduled sources. So for instance two different types of hardware interrupts might come in, but one (for instance an inter-processor-interrupt) could be put on a list for processing before the other (for instance a clock timer interrupt). Both of which are much higher priority than the normal background thread scheduling activity of context switching between threads which are blocked or which have used all their allotted time.

The `irql` command will let you examine what IRQL the context you're currently broken into was running at before the debugger took over. This can be important for knowing the likeliehood of code being preempted by higher IRQL code.

