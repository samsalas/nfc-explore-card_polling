# nfc-explore-card_polling
The source of the NFC explore card with some modifications (one to flush the stdout, one for working with the kernel 3.18 since 2015)
# The Raspberry advices!
To be able to work the NFC shield on the Raspberry Pi (mine is model Bv2), several things are good to know.
The basic is described here : http://www.framboise314.fr/jai-teste-pour-vous-la-carte-explore-nfc-delement-14-12/#comment-23647
But the card_polling sources are not enough to make these commands to work. You have to do
* Since kernel 3.18 (Beginning 2015)
The card-polling sample works on Raspbery Pi 2 with kernel 3.18, but there is a missing initialization after the line 329 of the file NxpRdLib_PublicRelease/comps/phbalReg/src/R_Pi_spi/phbalReg_R_Pi_spi.c :
<pre>
phStatus_t phbalReg_R_Pi_spi_Exchange(
phbalReg_R_Pi_spi_DataParams_t * pDataParams, 
uint16_t wOption, 
uint8_t * pTxBuffer, 
uint16_t wTxLength, 
uint16_t wRxBufSize, 
uint8_t * pRxBuffer, 
uint16_t * pRxLength
)
{
struct spi_ioc_transfer spi ;
<b>memset(&spi,0,sizeof(spi));   // add missing initialization</b>
</pre>
* For the python to get the result of the pooling, add fflush(stdout) in the polling loop in main.c, line 278
<pre>
        else
        {
            printf("No card or Tag detected\n");
        }
		fflush(stdout); // adding fflush to use python
        sleep(1);
    }

    phhalHw_FieldOff(pHal);
    return 0;
</pre>
