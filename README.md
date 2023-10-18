// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © jdehorty © veryfid

//@version=5

indicator(title='Kernel Regression Toolkit', shorttitle='kreg', overlay=true)
import veryfid/KernelFunctionsFilters/1 as kreg

// For more information on this technique refer to to the original open source indicator by @jdehorty located here: 
// https://www.tradingview.com/script/AWNvbPRM-Nadaraya-Watson-Rational-Quadratic-Kernel-Non-Repainting/

// Nadaraya-Watson Kernel Regression Settings
src = input(close)

filt1 = input.string("Smooth", options = ["No Filter", "Smooth", "Zero Lag"],title = "",group ="Kernel Regression 1 - Fast", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
type1 = input.string("Rational Quadratic", options = ["Rational Quadratic", "Gaussian", "Periodic", "Locally Periodic"],title = "",group ="Kernel Regression 1 - Fast", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
h = input.int(3, 'Lookback Window', minval=3, group="Kernel Regression 1 - Fast", inline="k")
r = input.float(1., 'Weighting', step=0.25, tooltip='Relative weighting of time frames. As this value approaches zero, the longer time frames will exert more influence on the estimation. As this value approaches infinity, the behavior of the Rational Quadratic Kernel will become identical to the Gaussian kernel. Recommended range: 0.25-25', group="Kernel Regression 1 - Fast", inline="k1")
x = input.int(2, "Level", tooltip='Bar index on which to start regression. Controls how tightly fit the kernel estimate is to the data. Smaller values are a tighter fit. Larger values are a looser fit. Recommended range: 2-25', group="Kernel Regression 1 - Fast", inline="k1")
lag = input.int(2, "Lag", tooltip="Lag for crossover detection. Lower values result in earlier crossovers. Recommended range: 1-2", inline='k1', group="Kernel Regression 1 - Fast")

filt2 = input.string("Smooth", options = ["No Filter", "Smooth", "Zero Lag"],title = "",group ="Kernel Regression 2 - Medium", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
type2 = input.string("Rational Quadratic", options = ["Rational Quadratic", "Gaussian", "Periodic", "Locally Periodic"],title = "",group ="Kernel Regression 2 - Medium", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
h2 = input.int(8, 'Lookback Window', minval=3, tooltip='The number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50', group="Kernel Regression 2 - Medium", inline="k")
r2 = input.float(1., 'Weighting', step=0.25, tooltip='Relative weighting of time frames. As this value approaches zero, the longer time frames will exert more influence on the estimation. As this value approaches infinity, the behavior of the Rational Quadratic Kernel will become identical to the Gaussian kernel. Recommended range: 0.25-25', group="Kernel Regression 2 - Medium", inline="kernel2")
x2 = input.int(20, "Level", tooltip='Bar index on which to start regression. Controls how tightly fit the kernel estimate is to the data. Smaller values are a tighter fit. Larger values are a looser fit. Recommended range: 2-25', group="Kernel Regression 2 - Medium", inline="kernel2")
lag2 = input.int(2, "Lag", tooltip="Lag for crossover detection. Lower values result in earlier crossovers. Recommended range: 1-2", inline='kernel2', group='Kernel Regression 2 - Medium')

filt3 = input.string("Smooth", options = ["No Filter", "Smooth", "Zero Lag"],title = "",group ="Kernel Regression 3 - Slow", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
type3 = input.string("Rational Quadratic", options = ["Rational Quadratic", "Gaussian", "Periodic", "Locally Periodic"],title = "",group ="Kernel Regression 3 - Slow", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
h3 = input.int(200, 'Lookback Window', minval=3, tooltip='The number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50', group="Kernel Regression 3 - Slow", inline="k")
r3 = input.float(1., 'Weighting', step=0.25, tooltip='Relative weighting of time frames. As this value approaches zero, the longer time frames will exert more influence on the estimation. As this value approaches infinity, the behavior of the Rational Quadratic Kernel will become identical to the Gaussian kernel. Recommended range: 0.25-25', group="Kernel Regression 3 - Slow", inline="kernel3")
x3 = input.int(500, "Level", tooltip='Bar index on which to start regression. Controls how tightly fit the kernel estimate is to the data. Smaller values are a tighter fit. Larger values are a looser fit. Recommended range: 2-25', group="Kernel Regression 3 - Slow", inline="kernel3")
lag3 = input.int(2, "Lag", tooltip="Lag for crossover detection. Lower values result in earlier crossovers. Recommended range: 1-2", inline='kernel3', group='Kernel Regression 3 - Slow')

filt4 = input.string("Smooth", options = ["No Filter", "Smooth", "Zero Lag"],title = "",group ="Kernel Regression 4 - Mono", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
type4 = input.string("Rational Quadratic", options = ["Rational Quadratic", "Gaussian", "Periodic", "Locally Periodic"],title = "",group ="Kernel Regression 4 - Mono", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
h4 = input.int(30, 'Lookback Window', minval=3, group="Kernel Regression 4 - Mono", inline="k")
r4 = input.float(8., 'Weighting', step=0.25, tooltip='Relative weighting of time frames. As this value approaches zero, the longer time frames will exert more influence on the estimation. As this value approaches infinity, the behavior of the Rational Quadratic Kernel will become identical to the Gaussian kernel. Recommended range: 0.25-25', group="Kernel Regression 4 - Mono", inline="k1")
x4 = input.int(35, "Level", tooltip='Bar index on which to start regression. Controls how tightly fit the kernel estimate is to the data. Smaller values are a tighter fit. Larger values are a looser fit. Recommended range: 2-25', group="Kernel Regression 4 - Mono", inline="k1")
lag4 = input.int(2, "Lag", tooltip="Lag for crossover detection. Lower values result in earlier crossovers. Recommended range: 1-2", inline='k1', group="Kernel Regression 4 - Mono")

filt5 = input.string("Smooth", options = ["No Filter", "Smooth", "Zero Lag"],title = "",group ="Kernel Regression 5 - Mono", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
type5 = input.string("Rational Quadratic", options = ["Rational Quadratic", "Gaussian", "Periodic", "Locally Periodic"],title = "",group ="Kernel Regression 5 - Mono", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
h5 = input.int(69, 'Lookback Window', minval=3, group="Kernel Regression 5 - Mono", inline="k")
r5 = input.float(1, 'Weighting', step=0.25, tooltip='Relative weighting of time frames. As this value approaches zero, the longer time frames will exert more influence on the estimation. As this value approaches infinity, the behavior of the Rational Quadratic Kernel will become identical to the Gaussian kernel. Recommended range: 0.25-25', group="Kernel Regression 5 - Mono", inline="k1")
x5 = input.int(500, "Level", tooltip='Bar index on which to start regression. Controls how tightly fit the kernel estimate is to the data. Smaller values are a tighter fit. Larger values are a looser fit. Recommended range: 2-25', group="Kernel Regression 5 - Mono", inline="k1")
lag5 = input.int(2, "Lag", tooltip="Lag for crossover detection. Lower values result in earlier crossovers. Recommended range: 1-2", inline='k1', group="Kernel Regression 5 - Mono")

filt6 = input.string("Zero Lag", options = ["No Filter", "Smooth", "Zero Lag"],title = "",group ="Kernel Regression 6 - Mono", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
type6 = input.string("Rational Quadratic", options = ["Rational Quadratic", "Gaussian", "Periodic", "Locally Periodic"],title = "",group ="Kernel Regression 6 - Mono", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
h6 = input.int(200, 'Lookback Window', minval=3, group="Kernel Regression 6 - Mono", inline="k")
r6 = input.float(1., 'Weighting', step=0.25, tooltip='Relative weighting of time frames. As this value approaches zero, the longer time frames will exert more influence on the estimation. As this value approaches infinity, the behavior of the Rational Quadratic Kernel will become identical to the Gaussian kernel. Recommended range: 0.25-25', group="Kernel Regression 6 - Mono", inline="k1")
x6 = input.int(500, "Level", tooltip='Bar index on which to start regression. Controls how tightly fit the kernel estimate is to the data. Smaller values are a tighter fit. Larger values are a looser fit. Recommended range: 2-25', group="Kernel Regression 6 - Mono", inline="k1")
lag6 = input.int(2, "Lag", tooltip="Lag for crossover detection. Lower values result in earlier crossovers. Recommended range: 1-2", inline='k1', group="Kernel Regression 6 - Mono")

ksrc = kreg.rationalQuadratic(close, 3, 1, 1, "No FIlter")
line1 = type1 == "Rational Quadratic" ? kreg.rationalQuadratic(src, h, r, x, filt1) : type1 == "Gaussian" ? kreg.gaussian(src, h-lag, x, filt1) : type1 == "Periodic" ? kreg.periodic(src, h, lag, x, filt1) : type1 == "Locally Periodic" ? kreg.locallyPeriodic(src, h, lag, x, filt1) : na
line2 = type2 == "Rational Quadratic" ? kreg.rationalQuadratic(src, h2, r2, x2, filt2) : type2 == "Gaussian" ? kreg.gaussian(src, h2-lag2, x2, filt2) : type2 == "Periodic" ? kreg.periodic(src, h2, lag2, x2, filt2) : type2 == "Locally Periodic" ? kreg.locallyPeriodic(src, h2, lag2, x2, filt2) : na
line3 = type3 == "Rational Quadratic" ? kreg.rationalQuadratic(src, h3, r3, x3, filt3) : type3 == "Gaussian" ? kreg.gaussian(src, h3-lag3, x3, filt3) : type3 == "Periodic" ? kreg.periodic(src, h3, lag3, x3, filt3) : type3 == "Locally Periodic" ? kreg.locallyPeriodic(src, h3, lag3, x3, filt3) : na
line4 = type4 == "Rational Quadratic" ? kreg.rationalQuadratic(src, h4, r4, x4, filt4) : type4 == "Gaussian" ? kreg.gaussian(src, h4-lag4, x4, filt4) : type4 == "Periodic" ? kreg.periodic(src, h4, lag4, x4, filt4) : type4 == "Locally Periodic" ? kreg.locallyPeriodic(src, h4, lag4, x4, filt4) : na
line5 = type5 == "Rational Quadratic" ? kreg.rationalQuadratic(src, h5, r5, x5, filt5) : type5 == "Gaussian" ? kreg.gaussian(src, h5-lag5, x5, filt5) : type5 == "Periodic" ? kreg.periodic(src, h5, lag5, x5, filt5) : type5 == "Locally Periodic" ? kreg.locallyPeriodic(src, h5, lag5, x5, filt5) : na
line6 = type6 == "Rational Quadratic" ? kreg.rationalQuadratic(src, h6, r6, x6, filt6) : type6 == "Gaussian" ? kreg.gaussian(src, h6-lag6, x6, filt6) : type6 == "Periodic" ? kreg.periodic(src, h6, lag6, x6, filt6) : type6 == "Locally Periodic" ? kreg.locallyPeriodic(src, h6, lag6, x6, filt6) : na

usej1 = input(false,"Show J line 1",group ="J Line 1", inline = "k")
jline1src1 = input.string("Line 2", options = ["Line 1", "Line 2", "Line 3", "Line 4", "Line 5", "Line 6","Close"],title = "",group ="J Line 1", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
jline1src2 = input.string("Line 4", options = ["Line 1", "Line 2", "Line 3", "Line 4", "Line 5", "Line 6","Close"],title = "",group ="J Line 1", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
jline1a = jline1src1 == "Line 1" ? line1 : jline1src1 == "Line 2" ? line2 : jline1src1 == "Line 3" ? line3 : jline1src1 == "Line 4" ? line4 : jline1src1 == "Line 5" ? line5 : jline1src1 == "Line 6" ? line6 : jline1src1 == "Close" ? close : jline1src1 == "Ksrc" ? ksrc : ksrc 
jline1b = jline1src2 == "Line 1" ? line1 : jline1src2 == "Line 2" ? line2 : jline1src2 == "Line 3" ? line3 : jline1src2 == "Line 4" ? line4 : jline1src2 == "Line 5" ? line5 : jline1src2 == "Line 6" ? line6 : jline1src2 == "Close" ? close : jline1src2 == "Ksrc" ? ksrc : ksrc 
jline1 = kreg.j(jline1a,jline1b)

usej2 = input(false,"Show J line 2",group ="J Line 2", inline = "k")
jline2src1 = input.string("Line 3", options = ["Line 1", "Line 2", "Line 3", "Line 4", "Line 5", "Line 6","Close", "J Line 1"],title = "",group ="J Line 2", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
jline2src2 = input.string("Line 5", options = ["Line 1", "Line 2", "Line 3", "Line 4", "Line 5", "Line 6","Close", "J Line 1"],title = "",group ="J Line 2", inline = "k", tooltip = "Select Kernel Regression type from dropdown box. The Lookback Window is the number of bars used for the estimation. This is a sliding value that represents the most recent historical bars. Recommended range: 3-50 ") 
jline2a = jline2src1 == "Line 1" ? line1 : jline2src1 == "Line 2" ? line2 : jline2src1 == "Line 3" ? line3 : jline2src1 == "Line 4" ? line4 : jline2src1 == "Line 5" ? line5 : jline2src1 == "Line 6" ? line6 : jline2src1 == "Close" ? close : jline2src1 == "J Line 1" ? jline1 : ksrc 
jline2b = jline2src2 == "Line 1" ? line1 : jline2src2 == "Line 2" ? line2 : jline2src2 == "Line 3" ? line3 : jline2src2 == "Line 4" ? line4 : jline2src2 == "Line 5" ? line5 : jline2src2 == "Line 6" ? line6 : jline2src2 == "Close" ? close : jline2src2 == "J Line 1" ? jline1 : ksrc 

jline2 = kreg.j(jline2a,jline2b)

useblend = input(false,"Show",group ="Blend Two Lines", inline = "k")
blendsrc1 = input.string("Line 2", options = ["Line 1", "Line 2", "Line 3", "Line 4", "Line 5", "Line 6","Close"],title = "",group ="Blend Two Lines", inline = "k", tooltip = "Select two Kernel Regression lines you would like to blend from the dropdown box. Adjust the percentage of the blend from 0-100%. It is also possible to overdrive the blend by pushing it over 100") 
blendsrc2 = input.string("Line 4", options = ["Line 1", "Line 2", "Line 3", "Line 4", "Line 5", "Line 6","Close"],title = "",group ="Blend Two Lines", inline = "k")
blenda = blendsrc1 == "Line 1" ? line1 : blendsrc1 == "Line 2" ? line2 : blendsrc1 == "Line 3" ? line3 : blendsrc1 == "Line 4" ? line4 : blendsrc1 == "Line 5" ? line5 : blendsrc1 == "Line 6" ? line6 : blendsrc1 == "Close" ? close : blendsrc1 == "Ksrc" ? ksrc : ksrc 
blendb = blendsrc2 == "Line 1" ? line1 : blendsrc2 == "Line 2" ? line2 : blendsrc2 == "Line 3" ? line3 : blendsrc2 == "Line 4" ? line4 : blendsrc2 == "Line 5" ? line5 : blendsrc2 == "Line 6" ? line6 : blendsrc2 == "Close" ? close : blendsrc2 == "Ksrc" ? ksrc : ksrc 
weight = input(50,"",group ="Blend Two Lines", inline = "k")
weightcalc = weight/100
blend = blenda*weightcalc + blendb * (1 - weightcalc)

plot(line1,"Line1", color= line1 > line1[1] ? color.teal : color.red, linewidth=2)
plot(line2,"Line2", color= line2 > line2[1] ? color.teal : color.red , linewidth=2)
plot(line3,"Line3", color= line3 > line3[1] ? color.teal : color.red , linewidth=2)
plot(line4,"Line4", color= color.gray, linewidth=1)
plot(line5,"Line5", color= color.gray, linewidth=1)
plot(line6,"Line6", color= color.gray, linewidth=1)

plot(usej1 ? jline1 : na,"j1",color = color.white)
plot(usej2 ? jline2 : na,"j2",color = color.white)

plot(useblend ? blend : na,"Blend")

