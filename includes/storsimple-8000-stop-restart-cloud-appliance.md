#### 若要將雲端設備停止和啟動
<a id="to-stop-and-start-a-cloud-appliance" class="xliff"></a>

1. 若要將雲端設備停止，請移至您雲端設備的 VM。
    ![StorSimple 雲端設備虛擬機器](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. 從命令列中，按一下 [停止]。

    ![StorSimple 雲端設備虛擬機器](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. 系統提示您進行確認時，按一下 [是] 。

    ![StorSimple 雲端設備虛擬機器](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. 當您停止 VM 時，它就會加以解除配置。 雲端設備停止時，其狀態為 [解除配置中]。 雲端設備停止後，其狀態為 [已停止 (已解除配置)]。

    ![StorSimple 雲端設備虛擬機器](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. 一旦 VM 停止後，按一下 [啟動] (按鈕會變成可用) 可將 VM 啟動。 雲端設備啟動後，其狀態為 [已啟動]。

    ![StorSimple 雲端設備虛擬機器](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

請使用下列 Cmdlet 將雲端設備停止和啟動。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### 若要將雲端設備重新啟動
<a id="to-restart-a-cloud-appliance" class="xliff"></a>

若要將雲端設備重新啟動，請移至您雲端設備的 VM。 從命令列中按一下 [重新啟動]。 出現提示時，確認重新啟動。 當雲端設備準備好可供您使用時，其狀態為 [執行中]。

![StorSimple 雲端設備虛擬機器](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

請使用下列 Cmdlet 將雲端設備重新啟動。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

